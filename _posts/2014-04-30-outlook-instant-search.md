---
layout: post
title: Outlook Instant Search
---

I often receive reports of Outlook Instant Search not returning complete search results.  This is often accompanied by the warning in Outlook that says "Search results may be incomplete because items are still being indexed."  It could sometimes take days or a week for emails in Outlook to finish indexing and then after a couple of days of Instant Search returning complete results, the search catalog would start the rebuild process again and Instant Search would return incomplete search results.

I took a look at the Application log on a computer experiencing this issue and noticed the following alert over and over:

```
Log Name:      Application 
Source:        Microsoft-Windows-Search
Event ID:      10024
Task Category: Gatherer
Level:         Warning
Description:
The filter host process 13100 did not respond and is being forcibly terminated.
```

It looked like something was causing the search process to crash.  I tried using my two favorite tools, Process Monitor and Process Explorer, to track down the cause, but I could not find the source of the issue.  After searching around for a solution, I came across a site talking about iFilters.  iFilters are plugins that allow search engines to index certain file formats.  It sounded like there could be an iFilter installed on these computers that was causing the search process to crash, but I needed away to figure out what iFilters were installed.

##Office Configuration Analyzer Tool
I found this very interesting tool named Office Configuration Analyzer Tool or OffCAT.  This tool returns a lot of useful information about the setup and configuration of Office including information about installed iFilters.  I ran the tool and sure enough under Configuration Details > Search > PDF iFilter > iFilter dll details, there was the following entry:

>Path =C:\Program Files(x86)\Xerox Scan To PC Desktop 11\PDF Professional 7\PDFProFilter.dll

It all made sense.  The computers having the issue all had Nuance PDF Professional installed and they all started having the search issue in Outlook shortly after the installation of Nuance PDF Professional.  I found an [article](http://nuance.custhelp.com/app/answers/detail/a_id/5626/~/pdfprofiltsrv.exe,-nuancewds.exe-and-microsoft-windows-desktop-search) on Nuance's website that mentions how their iFilter can sometimes cause issues with Windows Search.  To disable the iFilter, they recommend disabling the PDFProFiltSrv service and ending the NuanceWDS.exe process if it is running.  I also found that if you run a repair on Adobe Acrobat Reader, the Nuance PDF iFilter will be replaced by the Adobe Acrobat Reader iFilter.  You can also choose not to install the Nuance iFilter during the installation process by choosing the custom installation options.

After removing the Nuance PDF iFilter, an index that was taking days to rebuild would rebuild in under an hour and Outlook began returning complete search results.
