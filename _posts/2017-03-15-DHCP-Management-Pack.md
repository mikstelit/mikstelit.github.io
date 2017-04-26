---
layout: post
title: DHCP Management Pack
date: 2017-03-15
---

We recently ran out of IP addresses for one of our DHCP scopes.  I decided to install the DHCP management pack for SCOM so we could monitor our DHCP scopes and hopefully receive an alert before one of our DHCP scopes ran out of addresses again.  

The SCOM server is SCOM 2012 with no service packs.  The SCOM installation files I had access to during the installation of SCOM did not contain service pack 1.  Installing service pack 1 for SCOM 2012 was on my list of things to do and it should have been higher on my list as you will see later.

I read the documentation for the DHCP management pack and everything seemed straight forward.  I imported the DHCP management pack into SCOM and we soon received some alerts from SCOM about discovery scripts from the DHCP management pack failing:

```
System.Management.Automation.RuntimeException: Method invocation failed because [System.String[]] doesn't contain a method named 'Item'.
At line:67 char:20
+ $OS = $CurrVer.Item <<<< (0) + "." + $CurrVer.Item(1)
``` 

I looked at the discovery script in the DHCP management pack and the line generating the error was not doing anything unusual.  The section of the script in question uses WMI to get the OS version number and then does some manipulation of the OS version number to get the pertinent numbers.

I tested running the portion of the script on one of the DHCP servers and it ran without issue.  The error mentions System.String[] does not have a method called Item, but it clearly did as my test passed.  I did discover System.String[] does not contain a method called Item in PowerShell version 2.  This method seemed to have been introduced in PowerShell version 4.  The servers running the discovery script are Windows 2012 R2 servers which have PowerShell version 4 installed by default.

After doing some searching, I discovered the SCOM 2012 management agent uses PowerShell version 2 by default.  This would explain why the discovery script is generating errors on Windows 2012 R2 servers.  Service pack 1 for SCOM 2012 resolves this issue and allows the SCOM 2012 management agent to use a higher version of PowerShell if available.  I installed service pack 1 for SCOM 2012 and the DHCP servers were discovered without errors.  Lesson learned.  Patch your systems.