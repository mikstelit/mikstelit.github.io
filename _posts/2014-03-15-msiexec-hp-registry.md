---
layout: post
title: Msiexec.exe and HP Registry Keys
---


We have an old terminal server that was having trouble installing Windows Updates.  No matter what update you try to install, the msiexec.exe process would consume 100% of the CPU.  I was responsible for making sure this server was patched, so I had to figure out what the msiexec.exe process was doing.

There were no errors in the event log indicating any issues.  If you tried installing updates from the Windows Update client or manually downloading and installing the update, processor usage would spike to 100% and stay there.  I tried letting the installer run for an hour during a maintenance window, but the server was unable to install even one update during this time.  I decided to fire up Process Explorer and see if it could shed some light on the issue.

##Process Explorer

I did not see any child processes of msiexec.exe or anything out of the ordinary other than the 100% processor utilization.  I selected the option to show the lower pane and set the lower pane to display handles.  There were thousands of handles for a specific key in the Registry by msiexec.exe:

'''
HKLM\Software\Microsoft\Windows NT\Current Version\Terminal Server\Install\RefHive\Hewlett-Packard\
'''

I looked at this section of the registry and there were thousands of keys.  I did a quick search to find some information about this section of the registry and I found an article on HP's website saying this section of the registry can get populated with a large number of keys over time and that it is safe to delete the keys.  I created a backup of the keys and deleted them.  I then tried installing an update and it installed in a couple of seconds.  There does not appear to be any solution for this issue yet, other than to delete the registry keys periodically.
