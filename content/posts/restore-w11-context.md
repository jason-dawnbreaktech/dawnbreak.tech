+++
authors = ["JC Alan"]
title = "Restoring the Pre-W11 Right Click Context Menu"
date = "2024-11-12"
description = "Guide to restoring the pre-W11 right click context menu"
tags = [
    "Intune",
    "PowerShell",
    "SysAdmin",
]
categories = [
    "IT",
    "Systems Administration"
]
series = ["Intune"]
+++

# A welcome change... or not? 

In the following article, Microsoft described their mindset behind the context menu changes [here](https://blogs.windows.com/windowsdeveloper/2021/07/19/extending-the-context-menu-and-share-dialog-in-windows-11/).

To quote them directly:

> As useful as the Windows 10 context menu is, there are aspects of its design we sought to improve in Windows 11. The most common commands – cut, copy, paste, delete, and rename – are far from the mouse pointer, touch point, or pen.

When considering this, and especially when taking into account a computer with many applications installed, the default menu can be overwhelming:

![RightClickMenu](/images/w11-rightclick1.png)

It can also take much longer to load this classical context menu in Windows 11's redesigned file explorer (in this instance, upwards of 15-20 seconds). 

