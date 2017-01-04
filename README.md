Description
-------------------------------------------------------
Based on info from http://peterdowns.com/posts/open-iterm-finder-service.html but with modified behavior and fixed to work with iTerm2 version 3 or later. It will not work with older versions of iTerm. The modified behavior is to open a new terminal window for each invocation instead of reusing an already open window. Update - The original author released a build script for the newer iTerm2 versions at https://github.com/peterldowns/iterm2-finder-tools that keeps the original behavior of reusing an open iTerm2 window.

To open iTerm2 at selected folder with keyboard shortcut
--------------------------------------------------------
1. Run Automator, select a new Service
2. Select Utilities -> Double click ‘Run AppleScript’
3. Service receives selected 'folders' in 'finder.app'
4. Paste script:
    ```
    on run {input, parameters}
        tell application "Finder" to set dir_path to quoted form of (POSIX path of (first item of (get selection as alias list) as alias))
        CD_to(dir_path)
    end run

    on CD_to(theDir)
        tell application "iTerm"
            activate
            set win to (create window with default profile)
            set sesh to (current session of win)
            tell sesh to write text "cd " & theDir & ";clear"
        end tell
    end CD_to
    ```
5. Save as 'Open iTerm at Folder'
6. Open Keyboard in System Preferences. Under Shortcuts -> Services -> Files and Folders check 'Open iTerm at Folder' (with desired keyboard shortcut - e.g. control-option-command-T, making sure it's not already in use by another app). Note that sometimes macOS doesn’t register the keyboard shortcut and may require an OS restart to get it working.
7. To prevent two windows from opening when you use the shortcut keys on a selected Finder folder when iTerm2 is not already running, set iTerm2 -> Preferences -> Startup to "Don't Open Any Windows"

To make an Automator App that can reside on the Finder toolbar to open an iTerm2 window at the current directory
----------------------------------------------------------------------------------------------------------------
1. Run Automator, select new Application
2. Select Utilities -> Double click ‘Run AppleScript’
3. Paste script:
    ```
    on run {input, parameters}
        tell application "Finder"
            set dir_path to quoted form of (POSIX path of (folder of the front window as alias))
        end tell
        CD_to(dir_path)
    end run
    
    on CD_to(theDir)
        tell application "iTerm"
            activate
            set win to (create window with default profile)
            set sesh to (current session of win)
            tell sesh to write text "cd " & theDir & ";clear"
        end tell
    end CD_to
    ```
4. Save as 'iTermOpenScript.app' somewhere out of the way
5. Drag and drop the 'iTermOpenScript.app' onto the Finder toolbar while holding down the Option and Command keys to place the launch icon on Finder
6. To prevent two windows from opening when you click the launch icon when iTerm2 is not already running, set iTerm2 -> Preferences -> Startup to "Don't Open Any Windows"
7. If you want to replace the generic Automator icon, copy an image you want with \<Cmd\>\<C\>, then \<Cmd\>\<I\> on iTermOpenScript.app, select the application icon in the top left corner of the window and \<Cmd\>\<V\>.

pdanford - Jan 2017. MIT License, etc.
