---
layout: post
title: Xerox PBJ File
---


We have a reoccurring issue where programs will become unresponsive during general use and especially when performing a print preview.  The programs affected by the issue are usually Excel, Outlook, and Illustrator.  The users affected by the issue are usually all on the same floor.  The floor in question has two main printers that both use the Xerox 550-560 Integrated Fiery driver and most users on the floor have one of these printers as their default printer.  If you change the default printer for a user experiencing the issue to a printer not using the Xerox 550-560 Integrated Fiery driver, the issue goes away and if you reinstall the Xerox 550-560 Integrated Fiery printer driver, they can continue using their normal default printer.

Reinstalling the printer driver gets annoying after a while and it is even more annoying having a reoccurring issue that you have not found a solution for, so I took a deeper look into the issue when I had some free time.  The printer driver being used was the latest from Xerox.  The computers experiencing the issue did not have any recent changes and the event logs on the computers looked fine.  I decided to perform a Process Monitor capture on one of the computers experiencing the issue before I reinstalled the printer driver.

##Process Monitor
Process Monitor is by far my favorite tool when troubleshooting issues on Windows computers.  It gives so much insight into what is actually happening on the computer.  I opened the capture on my computer and performed the following steps:

1.  Go to Tools > Count Occurrences...
2.  From the Column: drop down, select Result
3.  Click the Count button

Right away I noticed there were 31 ACCESS DENIED occurrances for Result which seemed unusual.  I double clicked ACCESS DENIED to set a filter for the 31 events.  Each ACCESS DENIED event had the same data: 

```
Process Name: OUTLOOK.EXE
Operation: CreateFile
Path: C:\Windows\System32\spool\drivers\x64\3\EF472831.PBJ
Result: ACCESS DENIED
```
I checked the properties of the Xerox 550-560 Integrated Fiery driver and EF472831.PBJ is one of the files belonging to the driver.  The PBJ file appears to be an Adobe Pixel Bender Bytecode file.  The next time the issue occurred, I replaced the PBJ file on the computer having the issue with a PBJ file from a computer not having the issue and the unresponsive programs no longer had issues.  I started replacing the EF472831.PBJ on computers experiencing instead of reinstalling the driver as a quick fix to get the computers up and running again.  I am still working on finding the root cause of the issue and I will post any findings.


