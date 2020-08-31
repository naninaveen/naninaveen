---
toc: true

layout: post

title: Grouping custom scripts in windows right click contextmenu

description: A simple way to organize your scripts as a group in windows contextmenu.

image: images/python/contextmenu/right-click.png

permalink: /python/organizing-windows-contextmenu/

categories: [python, windows, contextmenu]
---
# Grouping custom scripts in windows right click contextmenu

## The problem
One fine boring day, I was cleaning unnecessary files in my PC and realized that there are too many empty folders.
Digging around my python scripts folders, I realized that once upon a time I wrote a script to clean empty folders.
The script takes a directory path as an argument.
But copying the directory path, launching the cmd prompt, and passing the directory path to the script is a waste of time.
While I was tinkering about a solution, I right-clicked on the folder and thought if only there is a menu item "Clean empty folders" that can run my script, that will solve my problem.

## Finding the solution
The next moment, I stopped deleting files and instead started searching on google about "Adding python scripts to the Windows context menu".
I found a few tutorials and understood that adding items to the context menu requires creating keys in the windows registry.
But thanks to the complexity of the Windows, the soutions I found seemed to be quite complex or less informative.
While most of the tutorials focused on adding single item to the context menu, grouping items into a folder like structure is still not an intuitive step.
It took me some experimentation and time to figure out how Windows actually deals with the registry keys when it comes to context menu.

There are atleast 15 registry keys where you can add context menu items.
1. `{BASE_ROOT}\\Directory\\shell\\` - to displays items when right click on a directory
1. `{BASE_ROOT}\\Drive\\shell\\` - to displays items when right click on a drive
1. `{BASE_ROOT}\\Directory\\Background\\shell\\` - to displays items when right click on empty space in the file explorer
1. `{BASE_ROOT}\\*\\shell\\` - to displays items when right click on any file(s)
1. `{BASE_ROOT}\\SystemFileAssociations\\{FILE_TYPE}\\shell\\` - to displays items when right click on a `FILE_TYPE` file type
1. `{BASE_ROOT}\\DesktopBackground\\shell\\` - to displays items when right click on desktop background

where `BASE_ROOT` can be `HKEY_CLASSES_ROOT`, `HKEY_CURRENT_USER\\Software\\Classes` or `HKEY_LOCAL_MACHINE\\SOFTWARE\\Classes`.
For adding items to all users `HKEY_CLASSES_ROOT` is used, and for adding items to the current user `HKEY_CURRENT_USER\\Software\\Classes` is used.

Now, each item in the context menu is associated with the following information.
1. Description of the item to be displayed on the context menu
1. Registry key associated with the item
1. Command to be executed
1. \[Optional\] Path to an icon

For grouping items into a folder like structure, the above details are required for each item along with
1. Name of the group
1. Registry key associated with the group
1. \[Optional\] Path to an icon

The structure of the registry keys and values for a single item is shown below.
```
ROOT
    ITEM_REG_KEY
        = (Default) = Name of the item
        = Icon = Path to an icon
        command
            = (Default) = Command
```
For a group of context menu items, the registry structure looks as follows.
```
ROOT
    GROUP_REG_KEY
        = MUIVerb = Group name
        = Icon = Path to an icon
        = Subcommands = 
        Shell
            ITEM1_REG_KEY
                = (Default) = Name of the item1
                = Icon = Path to an icon
                command
                    = (Default) = Command1
            ITEM2_REG_KEY
                = (Default) = Name of the item2
                = Icon = Path to an icon
                command
                    = (Default) = Command2
            SUBGROUP_REG_KEY
                = MUIVerb = Subgroup name
                = Icon = Path to an icon
                = Subcommands = 
                Shell
                    ITEM1_REG_KEY
                        = (Default) = Name of the item1
                        = Icon = Path to an icon
                        command
                            = (Default) = Command1
                    ...
            ...
```

## Implementation

### Test implementation
Let us first implement a simple script to add a menu item group "Group 1" and a single item "Item 1" to the group.
For adding registry keys we use python's `winreg` library.
```python
from winreg import (
    CreateKey, # for creating/opening keys
    SetValue, # for setting a key value
    SetValueEx, # for storing data in a given key
    HKEY_CURRENT_USER,
    REG_SZ, # to set value as a string
    CloseKey
)
```
Then create a key `HKEY_CURRENT_USER\\Software\\Classes\\Directory\\Background\\Shell\\TEST_ITEM`.
```python
itemreg_key = CreateKey(HKEY_CURRENT_USER, r"Software\\Classes\\Directory\\Background\\Shell\\TEST_ITEM")
# Set (Default) value to "Test item"
# "Test item" is shown in the context menu
SetValue(itemreg_key, '', REG_SZ, "Test item")
# Create and set (Default) value for command key
SetValue(itemreg_key, 'command', REG_SZ, "cmd.exe") # Opens a command prompt in the current directory
CloseKey(itemreg_key)
```

