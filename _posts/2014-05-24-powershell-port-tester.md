---
layout: post
title: PowerShell Port Tester
---

We recently changed the VPN solution used at work.  Some users have had issues using the VPN from certain networks.  They would receive errors saying the remote server could not be contacted.  I ruled out any DNS issues as possible causes and then thought it could have something to do with the port being used by the VPN client.

Our understanding was that the VPN client connected to the server using port 443 so there should be no issues traversing firewalls.  I wanted to confirm this so I fired up Process Monitor and connected using the VPN.  To my surprise, the VPN client was connecting using port 10443.  To confirm this, I tried telnetting to the VPN server using port 10443 and it connected.  When trying to connect to the VPN server using telnet and port 443, the connection failed.

I thought this could be the possible cause of some of the connection issues experienced by our users.  I wanted a simple way to test if a port was open on a remote system without having to install the telnet client.  I came up with this solution in PowerShell:

```
(New-Object Net.Sockets.TcpClient).Connect(“www.google.com”,”80”)
```

If the port is open and an application is behind it accepting connections, PowerShell will return to a blinking cursor.  If the connection fails, you will receive an error in PowerShell.  

I connected to a user’s computer that was experiencing the issue from home and my PowerShell test.  Sure enough, I was able to establish outbound connections to resources on ports 80 and 443, but not many other ports.  The user mentioned she had just received a new modem from Comcast that was an all-in-one firewall, router, and WAP.  I looked up this device and Comcast calls it a Wireless Gateway.  You can control what outbound ports are blocked on the device by raising and lowering the security level on the device.  This does not change any settings for inbound connections.  After lowering the security level, the PowerShell test worked on port 10443 to our VPN server and the VPN client began to connect.
