Launching iTerm2 via macOS Finder
=================================

The following describes ways to use the macOS Shortcuts.app to launch an iTerm2 window via Finder. These have been tested with iTerm2 version 3.7+ running on macOS Sonoma+.

pdanford - October 2026

### To Launch an iTerm2 Window:

### _at a selected folder in a focused Finder window - via keyboard hotkey_

This only works with a currently selected directory in a Finder window that has focus. (It also works when a file is selected. Also, if multiple directories are selected, there will be a new iTerm window for each.)

Starting sometime with iTerm2 version 3.x, it started providing **New iTerm2 Window Here** under the **Files and Folders** section at **System Settings → Keyboard → Keyboard Shortcuts → Services**. You just have to check it and assign a hotkey (e.g. <kbd>^</kbd><kbd>⌥</kbd><kbd>⌘</kbd><kbd>T</kbd>).

Note that sometimes macOS doesn't register a new keyboard shortcut assignment immediately and may even require an OS restart to get it working. But also make sure the keyboard shortcut is unique and not already assigned somewhere else.

### at the working folder of a Finder window (focused or not) - via Finder toolbar icon

To make a shortcut using the macOS **Shortcuts.app** that can reside on Finder's toolbar as an icon and that opens a new iTerm2 window at its working folder:

0. Open **Shortcuts.app** and check that running scripts is enabled (**Shortcuts → Settings… → Advanced → Allow Running Scripts**).
1. Create a new shortcut with the big plus and change the Title to e.g. _Open iTerm From Finder Toolbar_.
2. Click the **Action Library** button in the upper-right corner (its icon resembles a stack of pages). This opens the actions panel on the right.
    - In the panel's search field, type `run shell script`. Double-click or drag "Run Shell Script" into the shortcut's main workflow area as the first (and only) action. Fill in the following:
        - Run Shell Script:
        ```applescript
        WD=$(/usr/bin/osascript <<'APPLESCRIPT'
        tell application "Finder"
            try
                return POSIX path of (target of front Finder window as alias)
            on error
                display alert "Something went wrong:" message "Unable to discover Finder directory."
                return POSIX path of (path to home folder)
            end try
        end tell
        APPLESCRIPT
        )

        /usr/bin/open -a /Applications/iTerm.app "$WD"
        ```
        - Shell: `sh`
        - Input: `Input`
        - Pass Input: `to stdin`
    - Then close the new shortcut's edit window.
3. Right click on the newly created shortcut and click **Add to Dock**. This will make an app in your `~/Applications` directory with your shortcut name. We need this to make something that can be added to your Finder's toolbar. But it also makes it appear on you dock, so right-click it on the dock and **Options → Remove from Dock** since it won't be used that way.
4. Drag and drop **`~/Applications/Open iTerm From Finder Toolbar.app`** onto the Finder Toolbar while holding down the <kbd>⌘</kbd> key to place a toolbar launch icon. Note that the first time you launch there may be a security popup saying it wants Finder access... click OK.
5. To change the displayed icon of the app version of your new shortcut to be the same as iTerm's icon, select it in Finder in your `~/Applications` directory and right-click → **Get Info**. Do the same for the actual iTerm app (usually in `/applications`). To replace the generic shortcut app icon with the iTerm2 icon using these two info windows, select the application icon in the top left corner of the iTerm info window and <kbd>⌘</kbd><kbd>c</kbd>. Then select the application icon in the top left corner of the info window for the app version of your shortcut and <kbd>⌘</kbd><kbd>v</kbd>. Close the info windows.
6. Supporting iTerm2 settings:
    - To prevent two windows from opening when you click the Finder toolbar launch icon when iTerm2 is not already running, set iTerm2's **Preferences → General → Startup** to **Only Restore Hotkey Window** for the Window restoration policy.
    - To make windows open a bit faster,  in iTerm2's **Preferences → General → Closing** uncheck **Quit when all windows are closed**.

---

:scroll: [MIT License](README.license)
