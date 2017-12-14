---
layout: post
title: Updating DNS Settings Using SCCM
date: 2017-04-06
---

We recently replaced our DNS servers which meant we needed to update all systems to use the new DNS servers.  Usually, we could just update the DHCP server settings and that would take care of 95% of our systems, but we have several hundred systems that have statically assigned DNS servers.  I was tasked with finding a way to update the DNS settings on these systems with statically assigned DNS servers.  

My first thought was to use PowerShell and WMI to update the DNS settings, but the Windows Firewall is enabled on all of these systems and WMI connections are not allowed by our Windows Firewall settings.  I couldn't find a GPO to accomplish this task and I was running out of ideas.  I decided to try using SCCM to update the DNS settings on these systems.

I chose to use an Application in SCCM.  The major components of the Application are a simple PowerShell script that acts as the installation program and another PowerShell script that acts as the detection method.

Installation Program:  
```
$DNSServers = @(‘8.8.8.8’,’8.8.4.4’)

$NICs = Get-WmiObject -Query "Select * From win32_networkadapterconfiguration Where IPEnabled='True' and DHCPEnabled='False'"

Foreach ($NIC in $NICs)
{
    $NIC.setDNSServerSearchOrder($DNSServers) | Out-Null
}
```
Detection Method:
```
$DNSServers = @('8.8.8.8','8.8.4.4')

Try
{
    $NICs = Get-WmiObject -Query "Select * From win32_networkadapterconfiguration Where IPEnabled='True' and DHCPEnabled='False'"`
                          -ErrorAction Stop

    If ($NICs -eq $null)
    {
        Return $true
    }

    Foreach ($NIC in $NICs)
    {
        $CurrentDNSServers = $NIC.DNSServerSearchOrder

        $Difference = Compare-Object -ReferenceObject $CurrentDNSServers -DifferenceObject $DNSServers

        If(!$Difference)
        {
            Return $true   
        }
    }
}
Catch
{
    #Return nothing
}
```
This turned out to be a very simple and successful solution.  The ability to monitor the deployment of the Application was a huge benefit.  Out of the 900 systems that needed to be updated, only 7 had issues updating their DNS server settings and I was able to manually update those 7 systems.