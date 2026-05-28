---
title: "Calculating the size of the Windows kernel subsystems"
layout: single
date: 2025-06-15
tags: [posts, windows internals, windows kernel, reverse engineering, ida, ghidra]
excerpt: "Have you ever wondered which Windows kernel subsystem has the largest code size? Let's find out."
---

<img src="{{ site.url }}{{ site.baseurl }}/images/kernlsubsys1.jpg" alt="">

## Introduction

As we know, Windows has a proprietary hybrid kernel, written in C, C++, and Asm, called ntoskrnl.exe (also ntkrnlpa.exe, ntkrnlnmp.exe, depending on the installed version). It consists of various subsystems, each containing numerous functions. The term "subsystem" might suggest the presence of separate executable modules for each one. However, unlike operating systems with a microkernel architecture - which use different kernel executables for each subsystem and run them in separate processes - the Windows kernel subsystems are simply sets of functions within ntoskrnl, identified by specific name prefixes.

The names of these functions follow special rules (prefixes) that indicate their association with a particular subsystem. Without this naming convention, the Windows kernel would be a chaotic collection of unknown functions. Below, you can find more information about these prefixes along with explanation.

Since this post dedicated to the Windows Internals stuff, it implies a certain level of knowledge about this topic. If you don't want to dive deep into the Windows Internals book, you can use ChatGPT to find particular information you're interested in. This study also requires a good understanding of reverse engineering tools such as IDA Pro and Ghidra. We are going to use scripts for these tools to calculate the size of the Windows kernel subsystems.

Below is a diagram illustrating the kernel subsystems within ntoskrnl, proportionate to their sizes. It is based on the results of this study, calculated using the aforementioned IDA and Ghidra scripts. As we can see, the VMM is the largest subsystem, which is unsurprising since it plays a role in the operation of all other subsystems. Memory management is a critical component of any operating system.

<img src="{{ site.url }}{{ site.baseurl }}/images/kernlsubsys2.jpg" alt="">

Below are the the name prefixes of the subsystems included in the study.

⚙️ Ke, Ki - Kernel, Kernel Internal: the core, low-level Windows kernel functions

⚙️ Nt - high-level system services Nt*

⚙️ Io, Iop - I/O manager (Private)

⚙️ Mm, Mi - Memory Manager (Internal) : Virtual Memory Manager

⚙️ Cc - Cache Manager

⚙️ Cm, Cmp - Configuration Manager

⚙️ Po, Pop - Power Manager

⚙️ Ob, Obp - Object Manager

⚙️ Ex, Exp - Executive subsystem

⚙️ Se, Sep - Security Monitor

⚙️ Ps, Psp - Process Manager and Thread Manager

⚙️ Hal - responsible for the communication with HAL

⚙️ Etw - Event Tracing for Windows

⚙️ Pnp, Pi, Pip - Plug and Play Manager

⚙️ Ppm - Power Policy Manager

⚙️ Vf - Driver Verifier Framework

The executive subsystem stands out because it doesn't represent a specific module. Instead, it is designed to provide other subsystems with API that abstracts away the low-level details of the functions they rely on.

## The scripts

Our main goal is to calculate the size of the code for the Windows kernel subsystems. To achieve this, we need to identify the functions with the mentioned prefixes, analyze their code, and calculate their size. This can be done manually by downloading the PDB symbol file for ntoskrnl, applying the symbols to the executable, and calculating the size of the functions using open-source disassembler libraries. Alternatively, we can use the APIs provided by the libraries bundled with WinDbg to automate this process.

We choose the simpler approach by using the APIs provided by IDA Pro and Ghidra. First, we need to create the necessary IDB or project files, write a script, and run it. The logic of the script is straightforward: we iterate through all ntoskrnl functions, check their names for matching prefixes, and retrieve their sizes. Both reverse engineering tool offer the required API to facilitate this process. It's worth noting that IDA Free lacks the ability to run Python scripts due to the licensing limitations.

The Python interpreter bundled with Ghidra and IDA Pro is often outdated, so you might want to update it before using it within these tools. Unlike Ghidra, IDA Pro allows you to specify which installed Python version to use. It includes a tool called idapyswitch.exe, which detects installed Python versions and lets you choose the one you prefer. This tool is located in the IDA installation folder and is very convenient to use.

Omitting the details, the script for IDA Pro might look like this.

```
#Import two necessary IDA modules
import ida_funcs
import idc

#Define the list with the prefixes and the dictionary storing the total size of functions 
#belonging to each subsytem
prefixes = ['Ke', 'Ps', ...]
size_dict = {prefix: 0 for prefix in prefixes}

total_size = 0

#get_func_qty returns the total number of the functions within the open executable
for i in range(ida_funcs.get_func_qty()):
    func = ida_funcs.getn_func(i) #getn_func returns the context describing this func
    if not func:
        continue
        
    func_name = idc.get_func_name(func.start_ea) #get the function name from context
    func_size = func.end_ea - func.start_ea #calculate the function size from context

    total_size += func_size
    matched = False

    for prefix in prefixes: #check the function names on the prefixes
        if func_name.startswith(prefix):
            size_dict[prefix] += func_size
            matched = True
            break
    if not matched:
        size_dict['Others'] += func_size    
```

The Ghidra script looks a bit different.

```
from ghidra.program.model.listing import FunctionManager

fm = currentProgram.getFunctionManager()
functions = fm.getFunctions(True)

total_size = 0.0  # Use float to avoid integer division issues

for func in functions:
    func_name = func.getName()
    func_size = float(func.getBody().getNumAddresses())

    total_size += func_size
    matched = False

    for prefix in prefixes:
        if func_name.startswith(prefix):
            size_dict[prefix] += func_size
            matched = True
            break
     if not matched:
        size_dict['Others'] += func_size   
```
You can find the full versions of these scripts on my GitHub: [IDAPython](https://github.com/ArtemBaranov/IDAPython/blob/main/ntoskrnl_function_size.py), [GhidraPython](https://github.com/ArtemBaranov/GhidraPython/blob/main/ntoskrnl_function_size.py)

To launch the IDAPython script, open your IDB file and go to File->Script file... The script prints the results in the output window. In case of Ghidra, open ntoskrnl in CodeBrowser and go to Window->Script manager, Create New Script, next paste the text of the script and press Run Editor's Script.

The pictures below show information from the script output for ntoskrnl in the up-to-date Win11 23H2.

<img src="{{ site.url }}{{ site.baseurl }}/images/kernlsubsys3.jpg" alt="">

For a more visual representation, here are the statistics expressed as percentages. Additionally, you can see that 35% of the functions that do not belong to the explored subsystems.

<img src="{{ site.url }}{{ site.baseurl }}/images/kernlsubsys4.jpg" alt="">

## Conclusion

This simple study provides an approximate picture of the size of the major Windows kernel subsystems (jus for fun). As you can see, 35% of the code in ntoskrnl does not belong to any of the major subsystems, but that does not mean it isn't involved in their operation.

IDA and Ghidra offer comprehensive and complex API for exploring executables. The scripts above demonstrate just a few functions from their extensive capabilities. If you encounter difficulties with these APIs or with Python, you can ask ChatGPT for the necessary information or request an example script.