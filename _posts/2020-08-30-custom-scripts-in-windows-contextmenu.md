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
I found some tutorials and found that adding items to the context menu requires creating keys in the windows registry.
But as always, it took me a while to realize that there are more than 4 ways
