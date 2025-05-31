---
title: 'Remove Cached Report Data Ssrs'
date: 2019-09-05T23:48:24+01:00
draft: false
toc: false
---


I was working with a SQL Server Reporting Services (SSRS) Report the other day and found myself trying to work with some data changes while writing the report to ensure the report was behaving as expected. Initially, nothing on the report changed even though I had changed the data, I found this odd and thought I might’ve been pointing at the wrong database. After a bit of googling, I found that report data get cached after the first run of previewing a report.

In order to force the data to update, you must delete files from within the solution directory with the extension of “.rdl.data”. I can’t remember exactly which folder this is in, however it was getting tedious going into this folder and deleting those file all the time. As such, I had some fun playing around with the “External tools” section of Visual Studio and came up with the below.

If you click on Tools->External Tools in the top menu, click add give it a name of your choosing and use the following settings:-

- Command: %WinDir%\System32\cmd.exe
- Arguments: /C DEL /S /Q “$(SolutionDir)\*.rdl.data”

![Visual Stuaio Add External Tool](/ssrs-clear-data/ssrs-external-tools.png)

**WARNING: ONLY RAN ON WINDOWS – PLEASE GIVE A VS FOR MAC IMPLEMENTATION SO I CAN UPDATE IF YOU KNOW THE EQUIVELENT**

All this says is open up cmd.exe (Command Prompt) and run the command placed into the arguments. In this case, it will Delete all “.rdl.data” files within the solution directory, and it won’t echo it out.

You should now have a handy button in the Tools menu with whatever you called your external tool (in my case “Remove Cached Report Data”)


![Visual Studio Tool Added](/ssrs-clear-data/vs-tool-added.png)

Clicking this will now remove report data and you don’t have to worry about continuously jumping into the File Explorer and removing these files while testing reports, simply click this before each run and any data caching should be removed.

Hope this helps.