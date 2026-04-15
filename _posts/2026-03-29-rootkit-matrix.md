---
title: "Introducing the Rootkit Techniques Matrix and updates to the Guide"
layout: single
author_profile: false
classes: wide
date: 2026-03-29
tags: [rootkit, windows internals, windows kernel, ring 0]
excerpt: "Introducing the Rootkit Techniques Matrix and updates to the Guide"
toc: true
toc_label: "Table of Contents"
toc_icon: "list-ul"
---

## Introduction

Hello

Last year, I [published](https://github.com/ArtemBaranov/WindowsRootkitsGuide/blob/main/rootkits_bootkits_v2.pdf) a white paper called "Windows Rootkits and Bootkits Guide". The idea behind this document was to summarize kernel-mode techniques used by malware (rootkits and bootkits) to hide their presence in a compromised system. The guide also contains information about publicly known kernel-mode malware that utilize rootkit modules, with almost three hundred links to the research materials the information was taken from.

This paper and its [new version v2.1](https://github.com/ArtemBaranov/WindowsRootkitsGuide/blob/main/rootkits_bootkits_v2_1.pdf), which adds two new rootkit families, can be used as a reference to find information about existing rootkit families. However, the guide can't provide an interactive experience to navigate through rootkit techniques online. The Rootkit Techniques Matrix provides this experience, allowing to easily navigate all techniques and create a vector to refer to them. All information for the matrix was taken from the guide.

The idea for this matrix was inspired by the format of MITRE ATT&CK, but the number of TTPs in my case is much smaller. The text items in the matrix don’t contain their full descriptions like in ATT&CK; instead, I opted for tooltips. The text in the items is the technique ID, and the tooltips provide the description of the techniques. You need to hover the cursor over an item to see its description. To create a vector of techniques, a list of them with descriptions can be used for quicker reference. A vector can be copied into the input field, and the tooltips provide information about the appropriate already highlighted techniques. 

In this post, you can also find the matrices of rootkit and bootkit families and the techniques they use. All links to the materials used can be found in the paper.

## How To

<img src="{{ site.url }}{{ site.baseurl }}/images/rootkitmatrix.gif" alt="">

## The Rootkit Techniques

<div class="tree-view">
<ul>
  <li style="list-style: none">
    <details>
      <summary><strong>Rootkit techniques (Click to Expand)</strong></summary>
      
      <ul>
          <li style="list-style: none">
          <details>
            <summary>T1 Intercepting system services to control calls of basic Windows kernel functions</summary>            
            <ul>
              <li>T1.a Modifying SSDT (KiServiceTable)</li>
              <li>T1.b Modifying MSR_SYSENTER (IA32_SYSENTER_EIP, CS) for sysenter on x86 </li>
			  <li>T1.c Modifying KTHREAD.ServiceTable</li>
			  <li>T1.d Modifying IDT[0x2E] system service interrupt</li>
			  <li>T1.e Inline patching of KiSystemService or KiFastCallEntry (x86) </li>
			  <li>T1.f Inline patching of Nt* functions from SSDT</li>
            </ul>
          </details>
        </li>
		<li style="list-style: none">
          <details>
            <summary>T2 Direct Kernel Object Manipulation (DKOM) to manipulate Windows kernel structures</summary>          
            <ul>
              <li>T2.a Unlinking  drivers from PsLoadedModulesList (LDR_DATA_TABLE_ENTRY</li>
              <li>T2.b Unlinking processes from PsActiveProcessHead</li>
			  <li>T2.c Unlinking threads from KiWaitInListHead, KiWaitOutListHead, KiDispatcherReadyListHead</li>
			  <li>T2.d Modifying access token</li>
			  <li>T2.e Removing objects from Ob object directory</li>
			  <li>T2.f Removing driver objects from the list of driver objects</li>
			  <li>T2.g Removing device objects from the list of device objects</li>
			  <li>T2.h Forging	ETHREAD fields</li>
			  <li>T2.i Forging EPROCESS fields</li>
			  <li>T2.j Erasing	items in PspCidTable</li>
			  <li>T2.k Erasing	handles in the process handle table</li>
			  <li>T2.l Intercepting object type dispatch functions (procedures)</li>
			  <li>T2.m Forging DRIVER_OBJECT fields</li>
			  <li>T2.n Hijacking or spoofing driver object</li>
			  <li>T2.o Hijacking or spoofing device object</li>
            </ul>
          </details>
        </li>
		<li style="list-style: none">
          <details>
            <summary>T3 Inline patching kernel mode code (run-time patching, inline hooking, splicing)</summary>           
            <ul>
              <li>T3.a Ntoskrnl - * Nt*, IofCallDriver, IofCompleteRequest, IoCreateFile, etc</li>
              <li>T3.b FSD – Ntfs.sys, Fastfat.sys and attached filter, minifilter drivers (Filter Manager)</li>
			  <li>T3.c TCP/IP, NDIS - Tcpip.sys, Ndis.sys and its related internal structures</li>
			  <li>T3.d IP Filter Driver - Ipfilterdriver.sys</li>
			  <li>T3.e SCSI Class System Dll classpnp.sys</li>
			  <li>T3.f Disk port drivers - atapi.sys, ataport.sys, storport.sys, scsiport.sys</li>
			  <li>T3.g NULL Driver - Null.sys</li>
			  <li>T3.h ACPI Driver for NT – acpi.sys</li>
			  <li>T3.i MS QoS Packet Scheduler - psched.sys</li>
			</ul>
          </details>
        </li>
	    <li style="list-style: none">
          <details>
            <summary>T4 Intercepting driver object major functions or DriverUnload</summary>            
            <ul>
              <li>T4.a FSD – Fastfat.sys, Ntfs.sys to hide files</li>
              <li>T4.b TDI Tcpip.sys, Ndis.sys, also NDIS_OPEN_BLOCK and NDIS_PROTOCOL_BLOCK handlers</li>
			  <li>T4.c Disk port/miniport drivers - atapi.sys, ataport.sys, storport.sys, scsiport.sys</li>
			  <li>T4.d Fast I/O Dispatch Routine (FastIoDeviceControl) AfdFastIoDeviceControl of Afd.sys</li>
			  <li>T4.e Network Store Interface (NSI) driver nsiproxy.sys to hide TCP ports</li>
			  <li>T4.f NULL Driver - Null.sys to hide rootkit activity</li>
			  <li>T4.g LiveKd debugger driver</li>
			  <li>T4.h FS Filter Manager fltmgr.sys</li>
			  <li>T4.i Disk driver disk.sys</li>
			  <li>T4.j SCSI CD-ROM driver cdrom.sys</li>
			</ul>
          </details>
        </li>
	    <li style="list-style: none">
          <details>
            <summary>T5.a Intercepting IDT/ISR (excluding the case with hooking int 13h, which is used by almost all bootkits)</summary>          
          </details>
        </li>
		<li style="list-style: none">
          <details>
            <summary>T6 Setting up itself as a filter driver for (attaching to the device stack)</summary>
            
            <ul>
              <li>T6.a File System Driver (FSD), legacy or minifilter (fltmgr)</li>
              <li>T6.b Volume Manager (volmgr.sys, volmgrx.sys)</li>
			  <li>T6.c TCP/IP stack, NDIS (tcpip.sys, ndis.sys)</li>
			  <li>T6.d NSI driver nsiproxy.sys</li>
			</ul>
          </details>
        </li>
	    <li style="list-style: none">
          <details>
            <summary>T7 Using Windows kernel callbacks</summary>           
            <ul>
              <li>T7.a CmRegistry, LoadImageNotify, ObRegisterCallbacks</li>
			</ul>
          </details>
        </li>
		<li style="list-style: none">
          <details>
            <summary>T8.a Using and hiding NTFS Alternate Data Streams (ADS)</summary>          
          </details>
        </li>
		<li style="list-style: none">
          <details>
            <summary>T9.a "Keylogger (attaching to \\Device\\KeyboardClass0)</summary>          
          </details>
        </li>
		<li style="list-style: none">
          <details>
            <summary>T10.a "Windows IP Filtering</summary>          
          </details>
        </li>
		<li style="list-style: none">
          <details>
            <summary>T11 Disabling Windows kernel callbacks</summary>
             <ul>
              <li>T11.a LoadImageNotify, CreateThreadNotify, CreateProcessNotify, CmRegistry, ObRegisterCallback</li>
			</ul>
          </details>
        </li>
		<li style="list-style: none">
          <details>
            <summary>T12 Other tricks</summary>          
            <ul>
              <li>T12.a ObMakeTemporaryObject to remove the driver object’s name</li>
              <li>T12.b Disabling WFP callout drivers (via netio!gwfpGlobal)</li>
			  <li>T12.c Disabling Event Tracing (ETW) (via nt!EtwpHostSiloState)</li>
			  <li>T12.d Disabling System Loggers (via nt!EtwpActiveSystemLoggers)</li>
			  <li>T12.e Disabling FS minifilter drivers (via unlinking the appropriate structures)</li>
			  <li>T12.f Disabling Image Verification Callbacks</li>
			  <li>T12.g Hidden (Encrypted) File System (VFS)</li>
			  <li>T12.h Hiding services by unlinking the corresponding SERVICE_RECORD structure</li>
			  <li>T12.i Preventing writing kernel memory dumps by registering its callback with KeRegisterBugCheckReasonCallback</li>
			  <li>T12.j Replacing HHIVE.GetCellRoutine pointer to get control over system registry operations</li>
			  <li>T12.k Disabling FS minifilter drivers via FltUnregisterFilter</li>
			  <li>T12.l Implements its own private TCP/IP stack</li>
			  <li>T12.m Disables or bypasses PatchGuard</li>
			  <li>T12.n Files signed by a stolen cert (x86) (for x64 refer to [T14.c])</li>
            </ul>
          </details>
        </li>
		<li style="list-style: none">
          <details>
            <summary>T13 The subject of bootkit infection (to survive potential OS reinstallation)</summary>          
            <ul>
              <li>T13.a Master Boot Record (MBR)</li>
              <li>T13.b Volume Boot Record (VBR)</li>
			  <li>T13.c UEFI, the EFI System Partition (ESP)</li>
			  <li>T13.d UEFI, SPI flash memory</li>
			  <li>T13.e Legacy BIOS flash (by inserting a malicious ISA module)</li>
            </ul>
          </details>
        </li>
		<li style="list-style: none">
          <details>
            <summary>T14 Defeating DSE (for x64 only)</summary>          
            <ul>
              <li>T14.a BYOVD to (covertly) load the driver or patch the Windows kernel, DSE variables (g_CiEnabled)</li>
              <li>T14.b Signing the driver by a self-signed cert + reboot with TESTSIGNING bootloader flag</li>
			  <li>T14.c The driver has a valid digital signature (signed by a stolen cert)</li>
			  <li>T14.d Exploitation of a Windows kernel vulnerability</li>
			  <li>T14.e The bootkit loads the driver manually</li>
			  <li>T14.f The bootkit patches the Windows kernel</li>
            </ul>
          </details>
        </li>
      </ul>
    </details>
  </li>
</ul>
</div>

## The Matrix

Click on the desired techniques to expand them -> Select a subtechnique -> Copy vector to clipboard -> (Clear All) to reset the menu

Paste the vector from clipboard into the input field -> Click Show to highlight the techniques -> Hover the cursor over them ->Click "Reset" to reset the menu

{% include tech_accordion_nav.html %}

## The Rootkit Families Matrix

Click on the desired rootkit family to expand it -> Select subtechniques -> Copy vector to clipboard -> (Clear All) to reset the menu -> Go to Matrix -> Paste vector -> Click "Show"

{% include rootkit_accordion_nav.html %}

## The Bootkit Families Matrix

Click on the desired bootkit family to expand it -> Select subtechniques -> Copy vector to clipboard -> (Clear All) to reset the menu -> "Go to the matrix" -> Paste vector -> Click "Show"

{% include bootkit_accordion_nav.html %}

<style>
  /* Remove the max-width limit for the container */
  #main {
    max-width: 100% !important;
    padding: 0 2em; /* Add a little breathing room on the edges */
  }

  /* Force the page content to take up 100% of the available width */
  .page {
    width: 100% !important;
    padding-right: 0 !important;
    margin-right: 0 !important;
  }

  /* Re-align accordion columns horizontally */
  .accordion-nav-container {
    display: grid;
    grid-template-columns: repeat(auto-fit, minmax(250px, 1fr));
    gap: 20px;
    width: 100%;
  }

  /* Ensure each column stays contained */
  .accordion-column {
    width: 100%;
  }
</style>