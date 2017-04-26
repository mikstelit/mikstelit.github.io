---
layout: post
title: DNS Monitoring
date: 2017-04-25
---

We recently setup Windows Server 2012 R2 DNS servers.  These new DNS servers will be replacing our old Windows 2008 R2 DNS servers.  We updated our DHCP servers to hand out the new DNS servers and we used SCCM to update our systems that have statically assigned DNS servers, see previous post.  We are ready to shut down the old DNS servers, but we want to make sure the old DNS servers are not being used by any clients.  Luckily, there is an easy solution.

We can enable debug logging on our old DNS servers to log any incoming DNS queries received by the old DNS servers.  Follow these steps to enable debug logging on the DNS server:

	1.  Open DNS Manager on the server logging needs to be enabled.
	2.  Right click on the server and select Properties.
	3.  Click on the Debug Logging tab.
	4.  Select the following options:
		A.  Log packets for debugging
		B.  Packet direction: Incoming
		C.  Packet contents: Queries/Transfers
		D.  Transport protocol: UDP
		E.  Transport protocol: TCP
		F.  Packet type: Request
		G.  File path and name: C:\Temp\DNSLog.log
		H.  Maximum size (bytes): Enter a reasonable size.
	5.  Click OK

Check on the log file after a few hours.  The log file will show you the IP address of the client making the DNS request, the type of request, the hostname being queried, and more.  You can write a script to parse the log file and show you as list of the IP addresses of clients still making queries on the DNS servers.