---
layout: post
title: "Beat Win7 64bit Blue Screen"
date: 2012-06-03 16:18
comments: true
categories: [thinking in life]
---

这两天被折腾的够惨的，因为蓝屏总是时刻伴随着我，猝不及防，我了个丫的，我今天非把你给解决掉！
蓝屏代码：Stop: 0x000000C5 
>0X000000C5错误几乎总是由已损坏系统的驱动程序造成的，一般都是一些驱动程序出现故障，所以请尝试到计算机制造商下载安装最新版本的驱动程序安装更新。

现象：  
1. 当我尝试运行迅雷7进行网络监测时直接进入蓝屏  
2. 当我运行酷狗音乐盒一段时间后，然后让电脑睡眠，过一段时间接着回到运行界面，后面你懂得  

排错过程：   
1. 首先我的机器是全新的，排除硬件问题   
2. 那一定就是软件的驱动问题   
3. 根据现象一，起初我认为是迅雷导致，于是我把迅雷卸载掉，情况依旧   
4. 情况不容乐观，看来不能只看表面现象，于是我请来了windows的[debugger tool](http://msdn.microsoft.com/en-us/windows/hardware/gg463009)和[symbol库](http://http//msdn.microsoft.com/en-us/windows/hardware/gg463028)  
5. 使用tool来分析问题，win->windbg 运行tool  
6. 导入symbol库，“File→Symbol Search Path”，输入 "SRV*c:\sytemp*http://msdl.microsoft.com/download/symbols"。  
7. 导入.dmp文件(windows的crash记录文件)，一般位于C:\Windows\MEMORY.DMP 或 C:\Windows\Minidup文件夹中，“File（文件）”－“Open Crash Dump（打开内存转储文件）”，然后选择路径就可以了  
内容如下：  
	~ Microsoft (R) Windows Debugger Version 6.2.8400.0 AMD64  
	Copyright (c) Microsoft Corporation. All rights reserved.  
	Loading Dump File [C:\Windows\Minidump\060312-17050-01.dmp]  
	Mini Kernel Dump File: Only registers and stack trace are available  
	Symbol search path is: D:\Symbols  
	Executable search path is:   
	Unable to load image \SystemRoot\system32\ntoskrnl.exe, Win32 error 0n2  
	*** WARNING: Unable to verify timestamp for ntoskrnl.exe  
	*** ERROR: Module load completed but symbols could not be loaded for ntoskrnl.exe  
	Windows 7 Kernel Version 7601 (Service Pack 1) MP (8 procs) Free x64  
	Product: WinNt, suite: TerminalServer SingleUserTS  
	Built by: 7601.17640.amd64fre.win7sp1_gdr.110622-1506  
	Machine Name:  
	Kernel base = 0xfffff800`04067000 PsLoadedModuleList = 0xfffff800`042ac670  
	Debug session time: Sun Jun  3 11:07:51.221 2012 (UTC + 8:00)  
	System Uptime: 0 days 0:02:42.080  
	Unable to load image \SystemRoot\system32\ntoskrnl.exe, Win32 error 0n2...  ~    
* 发现[ntoskrnl.exe](http://baike.baidu.com/view/449849.htm)加载错误,这个错误一般是在系统启动加载阶段导致  
* 由于信息不够，需要进一步查询：使用 “!analyze -v ”命令继续查找，信息如下：   
	
	DRIVER_CORRUPTED_EXPOOL (c5)
	An attempt was made to access a pageable (or completely invalid) address at an
	interrupt request level (IRQL) that is too high.  This is
	caused by drivers that have corrupted the system pool.  Run the driver
	verifier against any new (or suspect) drivers, and if that doesn't turn up
	the culprit, then use gflags to enable special pool.
	Arguments:
	Arg1: 0000000000002f2c, memory referenced
	Arg2: 0000000000000002, IRQL
	Arg3: 0000000000000000, value 0 = read operation, 1 = write operation
	Arg4: fffff80004212dd3, address which referenced memory
	Debugging Details:

	*** WARNING: Unable to verify timestamp for VBoxNetFlt.sys
	*** ERROR: Module load completed but symbols could not be loaded for VBoxNetFlt.sys
	... ...
	FOLLOWUP_IP: 
	VBoxNetFlt+3aca
	fffff880`059bfaca ??              ???
	SYMBOL_STACK_INDEX:  5
	SYMBOL_NAME:  VBoxNetFlt+3aca
	FOLLOWUP_NAME:  MachineOwner
	MODULE_NAME: VBoxNetFlt
	IMAGE_NAME:  VBoxNetFlt.sys
	DEBUG_FLR_IMAGE_TIMESTAMP:  4f86ff00
	FAILURE_BUCKET_ID:  X64_0xC5_2_VBoxNetFlt+3aca
	BUCKET_ID:  X64_0xC5_2_VBoxNetFlt+3aca
	Followup: MachineOwner

* 这下豁然开朗了，原来是VBoxNetFlt.sys导致问题，想起前段时间确实更新了一次Virtual Box，于是果断卸载virtual box，这下天下太平了！阿门！
	


