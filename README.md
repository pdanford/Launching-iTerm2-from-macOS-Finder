Launching iTerm2 from macOS Finder
==================================

(Based on info from [Peter Downs' gitub](https://github.com/peterldowns/iterm2-finder-tools) but with modified behavior to open a new terminal window for each invocation instead of reusing an already open window.)

The following three ways to launch an iTerm2 window from Finder have been tested on iTerm2 version 3+ running on macOS Mojave+.

pdanford - April 2020

---
### From a selected directory in a Finder window - via keyboard Shortcut
Starting sometime with iTerm2 version 3.x, it puts **New iTerm2 Window Here** in `System Preferences -> Keyboard -> Shortcuts -> Services` under the `Files and Folders section`. You just have to check it and `Add Shortcut`.

Note that sometimes macOS doesn’t register a new keyboard Shortcut assignment immediately and may even require an OS restart to get it working. But also make sure the keyboard Shortcut is unique and not already assigned somewhere else.

This is only active when a folder is currently selected in a Finder window that has focus.

### From the working directory of a Finder window - via keyboard Shortcut
1. Run Automator, `File -> New` and choose **Quick Action**.
2. Click `Library -> Utilities` and double click **Run AppleScript**.
3. Workflow receives **Automatic (Nothing)** in **any application**.
4. Paste the script from [AppleScript](#AppleScript) section below.
5. Save; if Automator tries to save to iCloud drive, better to save locally - save as **iTermOpenHereScript.workflow** to the Desktop and then move it to **~/Library/Services**.
6. Add the keyboard shortcut under `System Preferences -> Keyboard -> Shortcuts -> Services` under the `General` section. Check **iTermOpenHereScript** and Add Shortcut. Note that the first time you launch there may be a security popup saying iTermOpenHereScript wants access... Click OK to all.
7. To prevent two windows from opening when you use the keyboard Shortcut when iTerm2 is not already running, set iTerm2's `Preferences -> General -> Startup` to **Only Restore Hotkey Window** for the Window restoration policy.
8. To make windows open a bit faster,  in iTerm2's `Preferences -> General -> Closing` uncheck **Quit when all windows are closed**.

Note that sometimes macOS doesn’t register a new keyboard Shortcut assignment immediately and may even require an OS restart to get it working. But also make sure the keyboard Shortcut is unique and not already assigned somewhere else.

This is only active when a Finder window (or the Desktop itself) has focus (and ignores any selected Finder directory).

The Desktop having focus isn't really the intended use case, but for completeness, if it's the Desktop that has focus the working directory of its last focused still-open Finder window is used. If the current Desktop does not have any Finder windows, it's a bit indeterministic, but a working directory from a Finder window in other Space is used. If there are no Finder windows open in any other Spaces, it will default to the user's home directory.

### From the working directory of a Finder window - via Finder toolbar icon
1. Run Automator, `File -> New` and choose **Application**.
2. Click `Library -> Utilities` and double click **Run AppleScript**.
3. Paste the script from [AppleScript](#AppleScript) section below.
4. Save; if Automator tries to save to iCloud drive, better to save locally - save as **iTermOpenHereScript.app** to the Desktop and then move it to **~/Library/Services**.
5. Drag and drop iTermOpenHereScript.app onto the Finder toolbar while holding down the <kbd>option</kbd>+<kbd>command</kbd> keys to place a toolbar launch icon. Note that the first time you launch there may be a security popup saying iTermOpenHereScript wants access... Click OK to all.
6. Right click on the spot on the toolbar and select **Icon Only**. To replace the generic Automator app icon with the iTerm2 icon, <kbd>command</kbd>+<kbd>i</kbd> on the original iTerm.app file, select the application icon in the top left corner of the window and <kbd>command</kbd>+<kbd>c</kbd>. Then do <kbd>command</kbd>+<kbd>i</kbd> to iTermOpenHereScript.app file, select the application icon in the top left corner of the window and <kbd>command</kbd>+<kbd>v</kbd>.
7. To prevent two windows from opening when you click the toolbar launch icon when iTerm2 is not already running, set iTerm2's `Preferences -> General -> Startup` to **Only Restore Hotkey Window** for the Window restoration policy.
8. To make windows open a bit faster,  in iTerm2's `Preferences -> General -> Closing` uncheck **Quit when all windows are closed**.

---
### AppleScript

    on run {input, parameters}
        set frontApp to (path to frontmost application as Unicode text)
        if (frontApp does not contain "Finder.app") then
            -- Finder does not have focus.
            return
        end if

        tell application "Finder"
            set listSize to count of (every window)
            if listSize is equal to 0 then
                -- The Finder desktop has focus and no windows anywhere else. default to home dir.
                set dir_path to "~"
            else
                try
                    set dir_path to quoted form of (POSIX path of (folder of the front window as alias))
                on error errMsg
                    -- This is a special dir (e.g. Network or "machine name"). default to home dir.
                    set dir_path to "~"
                end try
            end if
        end tell

        CD_to(dir_path)
    end run

    on CD_to(theDir)
        tell application "iTerm"
            set term_window to (create window with default profile)
            set sesh to (current session of term_window)
            tell sesh to write text "cd " & theDir & ";clear"
        end tell
    end CD_to

---
:scroll: [MIT License](README.license)
