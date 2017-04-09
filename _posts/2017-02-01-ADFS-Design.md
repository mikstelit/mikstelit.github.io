---
layout: post
title: AD FS Infrastructure Design
date: 2017-02-01
---

My company was making the move to Office 365 and we decided to use AD FS for single sign-on. I volunteered to design and setup our AD FS infrastructure. We needed our AD FS infrastructure to be highly available, but also not cost a ton of money which meant utilizing tools built into Windows Server.

The plan was to place two AD FS farm servers and two AD FS proxy servers in our primary datacenter. The AD FS farm servers and AD FS proxy servers would use Windows Network Load Balancing to provide some redundancy. This is a pretty standard AD FS setup, but I needed to come up with a solution that would allow us to continue providing AD FS services if our primary datacenter became unavailable.

We happen to have an office in another continent that already hosts some services for our users on that continent. I decided to setup an identical AD FS infrastructure in the office on another continent. This other office is connected to our datacenter using an MPLS network which means the AD FS farm servers at our datacenter would be able to communicate with the AD FS farm servers at the second office. This enabled me to setup an AD FS farm with four servers and our primary server would be located in our datacenter. Here is a diagram of the AD FS infrastructure:

!(/public/pics/ADFS_Diagram.png){:class="img-responsive"}

Under normal circumstances, all AD FS services run through the AD FS servers in the datacenter. If something happens to one of the AD FS farm servers at the datacenter or AD FS proxy servers at the datacenter, Windows Network Load Balancing will direct all requests to the server still online. If there is an outage to both AD FS farm servers at the datacenter or both AD FS proxy servers at the datacenter or an entire outage of the datacenter, we just need to change the internal DNS record and external DNS records to point to the AD FS infrastructure located at the remote office.