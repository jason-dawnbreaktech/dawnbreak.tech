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

**tldr: if you just want to revert the right click menu, skip to the 'Identifying the Registry Key' section**

In the following article, Microsoft described their mindset behind the context menu changes [here](https://blogs.windows.com/windowsdeveloper/2021/07/19/extending-the-context-menu-and-share-dialog-in-windows-11/).

To quote them directly:

> As useful as the Windows 10 context menu is, there are aspects of its design we sought to improve in Windows 11. The most common commands – cut, copy, paste, delete, and rename – are far from the mouse pointer, touch point, or pen.

The Windows 11 context menu addresses these problems in the following ways:
- Common commands are placed right next to where the menu is invoked.
- “Open” and “Open with” are grouped together.

When considering this, and especially when taking into account a computer with many applications installed, the default menu can be overwhelming:

![RightClickMenu](/images/w11-rightclick1.png)

It can also take much longer to load this classical context menu in Windows 11's redesigned file explorer (in this instance, upwards of 15-20 seconds). The logic behind the change is sound, but humans are creatures of habit, and these logical changes were paired with the idea to turn some of the most commonly used options (such as copy/paste) into symbols rather than text. 

If Windows 11 was your first ever Windows OS, this might be easier to pick up, but for any long time user of the operating system, this is a lot to change at once. Personally, I would have opted for a staged rollout and introduced the symbols at a later date. 

### Identifying the Registry Key

To restore the pre-W11 right click menu, add the following registry key:

- `HKCU\Software\Classes\CLSID\{86ca1aa0-34aa-4e8b-a509-50c905bae2a2}\InprocServer32`

This can be accomplished in a few seconds by opening a command prompt, and pasting in the following:

``` cmd
reg add "HKCU\Software\Classes\CLSID\{86ca1aa0-34aa-4e8b-a509-50c905bae2a2}\InprocServer32" /f /ve

taskkill /f /im explorer.exe

start explorer
```

![CMD](/images/rightclick-cmd1.png)

Note that this will restart explorer to enact the change. 

Make sure that the value is an **empty string**, and not **(value not set)**. 

![Registry](/images/rightclick-registry1.png)



# For the System Administrators

Recently, I took over an environment which was stuck in the past,  with antiquated systems in too many places. As part of a larger initiative to modernize the environment, and for various compliance reasons, we upgraded all of our end users to Windows 11 . Of course, the end user complaints about the new right click context menu were numerous. I wanted to give my users a way to revert the change easily, so I decided to create a Win32 package that would add this registry key. This accomplished a few things:

- It made the change optional, and users could turn it on whenever they pleased. 
- It gave us a way to administratively undo the change if, for whatever reason, this registry key caused issues down the road. 
- It drove adoption of the **Company Portal**, which I had been pushing with limited success.

See the following GitHub repo for an assortment of PowerShell scripts that will accomplish the above steps. To build a Win32 package for deployment via the company portal:

1. Clone the repo. 
2. Use the IntuneWinAppUtil to package the directory. 
3. Upload the .intunewin file to Intune with the following `install` command:
``` cmd
powershell -ex bypass -file .\Restore-W10RightClick.ps1
```
4. Set the installation package to run in the **user** context. 
5. For the detection method, use the registry key. 
6. I've marked the uninstall as available for end users. An important note about Intune uninstall commands: **they always run as system, regardless of the context set during the install phase**. This is problematic for the 'uninstall' of this change, as the key exists in the current user hive, which can't be directly referenced from the NT System Authority context. 
7. To solve this, use the following uninstall command:
``` cmd
powershell -ex bypass -file .\Restore-W11RightClick-Intune.ps1
```
8. The above script iterates through *all* of the keys in the HKEY_USERS hive and deletes the registry key if it exists. This avoids the 'HKCU' issue, but note that the command will restore the default W11 right click context menu for **all users** on the system.
9. The end result is a visually appealing option in the Company Portal for end users to engage with. 

