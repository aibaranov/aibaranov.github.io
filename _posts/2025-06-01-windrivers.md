---
title: "Lift me up to Ring 0: what are the most vulnerable Windows drivers"
layout: single
date: 2025-06-02
tags: [posts, vulnerability research, Windows, security]
excerpt: "Examining the statistics on the most frequently patched Windows drivers between January 2022 and May 2025"
---
Decided to go through Microsoft's CVE portal over the past three years (January 2022 - May 2025) to find out which Windows km components have been patched most frequently. The charts with the statistics are presented below, and some of the findings may look surprising. 

Windows drivers are known to be one of the main sources of LPE (EoP) vulnerabilities in the OS. Unlike flaws in system services that allow privilege escalation to Administrator or even System - but in user mode - vulnerabilities in drivers enable execution of user-mode code directly in kernel mode. These include not only LPE vulnerabilities, but also ID, allowing km memory leak, bypass, bypassing KASLR, or, even, RCE, remote code execution in kernel mode.

<img src="{{ site.url }}{{ site.baseurl }}/images/driver1.jpg" alt="">

This chart is dedicated to the most frequently patched Windows drivers.

Determining which executables have been patched has become much harder after Microsoft changed the format of its security (CVE) bulletins (advisories), removing almost all technical information - including the file names of patched files. Some of them are well-known to security folks who have been tracking Microsoft's security portal for a long time. However, a new category called "Windows Drivers" has appeared, acting as a black box designed to obscure all details about the drivers that have been fixed.

<img src="{{ site.url }}{{ site.baseurl }}/images/driver3.jpg" alt="">

This one shows statistics for the less frequently patched drivers.

<img src="{{ site.url }}{{ site.baseurl }}/images/driver4.jpg" alt="">

Typically, nearly all vulnerabilities fixed on Patch Tuesdays are responsibly reported to Microsoft by security researchers. However, some may be publicly disclosed or reported by anti-malware companies, indicating that they are being exploited in-the-wild. It is also common for Microsoft to release out-of-band security updates for vulnerabilities that are actively exploited and considered critically dangerous.

<img src="{{ site.url }}{{ site.baseurl }}/images/driver2.jpg" alt="">

This chart shows statistics about actively exploited vulnerabilities.

Even though we've seen a large number of discovered and fixed vulnerabilities in Windows drivers, only a few have been observed as exploited in real-world attacks. This is because Microsoft and Intel have invested significant effort in  implementing various security mitigations against common malware exploitation techniques. These include mitigations for null pointer dereference, as well as features like DEP, KASLR, SMEP, SMAP, and PatchGuard. As a result, even when attackers discover a vulnerability, they have limited chances of successfully exploiting it.

<img src="{{ site.url }}{{ site.baseurl }}/images/driver5.jpg" alt="">
Vulnerabilities exploited in-the-wild.

Due to the aforementioned mitigations, in recent years we've seen attackers shift from exploiting vulnerabilities in Windows drivers to using BYOVD. In this method, malware utilizes 3rd party drivers susceptible to unauthorized code execution. However, this technique has notable disadvantages, as typically cannot be used without first obtaining administrator privileges. One way or another, an attacker must have admin rights to enable SeLoadDriverPrivilege required to load a vulnerable driver.

The table below provides brief information about the mentioned drivers.

<img src="{{ site.url }}{{ site.baseurl }}/images/driver6.jpg" alt="">

<img src="{{ site.url }}{{ site.baseurl }}/images/driver7.jpg" alt="">

<img src="{{ site.url }}{{ site.baseurl }}/images/driver8.jpg" alt="">

Nevertheless, there is a way to determine which drivers were patched on Patch Tuesdays. Windows stores previous versions of drivers in the System32 folder, and by checking their creation or modification dates, it is possible to determine when the updated version of a driver arrived. However, this way has its pitfalls. Since quality updates are also delivered on the same day, it could be a false flag. Earlier, I published a video on YouTube that demonstrates this approach for identifying the necessary version of Tcpip.sys for diffing and locating the source of a vulnerability.

[![Alt text](https://img.youtube.com/vi/bfuG355in80/0.jpg)](https://www.youtube.com/watch?v=bfuG355in80)