﻿---
layout: post
title: Linux resources manage tools
date: 2024-07-18 15:53:00-0400
description: record common vscode configuration
tags: linux
categories: system
featured: true
toc:
  beginning: true
  sidebar: left
---

首先我们了解一下系统中需要查询的基本资源信息，比如查看系统的`硬件资源状态`,`进程状态`,`内存使用`,`cpu使用`,`服务状态`,`端口状态`。  
常用工具一般有实时的和非实时的。在紧密监视系统进程变化时，实时的工具对我们非常有用。 
此文主要结合opensuse文档和自己的使用经验。   

# Multi-purpose tools

## vmstat
vmstat 是一个可以展示有关进程，内存，分页，块设备IO,陷阱，磁盘和cpu活动的综合性工具。在Fedora中位于coreutil软件包中，在Debian/Ubuntu中位于sysstat软件包内。输出如下：  

```
procs -----------memory---------- ---swap-- -----io---- -system-- -------cpu-------
 r  b   swpd   free   buff  cache   si   so    bi    bo   in   cs us sy id wa st gu
 1  1      0 8229040   5500 4186244    0    0    26    53  234    2  1  1 98  0  0  0
```
输出内容从左到有分别是`正在运行的进程状态`,`内存使用状态`,`内存交换空间`,`快设备IO`,`系统中断状态`,`cpu消耗`状态。 
cli内直接输入`vmstat`输出的信息是自启动以来的平均值,可以衔接`[delay [count] ]`参数间隔时间采集信息。  
```
➜  ~ vmstat 1 
procs -----------memory---------- ---swap-- -----io---- -system-- -------cpu-------
 r  b   swpd   free   buff  cache   si   so    bi    bo   in   cs us sy id wa st gu
 2  1      0 8489208   5500 4149584    0    0    26    57  265    3  2  1 98  0  0  0
 0  0      0 8518180   5500 4125708    0    0     0  4704 2868 1620  0  1 98  1  0  0
 0  0      0 8541876   5500 4116808    0    0     0   608 1442 1017  0  0 99  1  0  0
 2  0      0 8536144   5500 4113104    0    0     0     0  324  387  0  0 100  0  0  0
 0  0      0 8539732   5500 4112700    0    0     0     0  474  581  0  0 99  0  0  0
^C
```
```
Usage:
 vmstat [options] [delay [count]]

Options:
 -a, --active           active/inactive memory
 -f, --forks            number of forks since boot
 -m, --slabs            slabinfo
 -n, --one-header       do not redisplay header
 -s, --stats            event counter statistics
 -d, --disk             disk statistics
 -D, --disk-sum         summarize disk statistics
 -p, --partition <dev>  partition specific statistics
 -S, --unit <char>      define display unit
 -w, --wide             wide output
 -t, --timestamp        show timestamp
 -y, --no-first         skips first line of output

 -h, --help     display this help and exit
 -V, --version  output version information and exit
```
> 第一行输出永远是重启来的平均值  

## dstat
专业的实时工具,你可以用它来实时分析磁盘吞吐和网络吞吐量。默认采集间隔1s。  Fedora系统在`pcp-system-tools
```
➜  ~ dstat --help 
Usage: dstat [-afv] [options...] [delay [count]]
Versatile tool for generating system resource statistics

Dstat options:
  -c, --cpu             enable cpu stats
     -C 0,3,total          include cpu0, cpu3 and total
  -d, --disk            enable disk stats
     -D total,sda          include sda and total
  --dm, --device-mapper enable device mapper stats
     -L root,home,total    include root, home and total
  --md, --multi-device  enable multi-device driver stats
     -M total,md-0         include md-0 and total
  --part, --partition   enable disk partition stats
     -P total,sdb2         include sdb2 and total
  -g, --page            enable page stats
  -i, --int             enable interrupt stats
     -I 9,CAL              include int9 and function call interrupts
  -l, --load            enable load stats
  -m, --mem             enable memory stats
  -n, --net             enable network stats
     -N eth1,total         include eth1 and total
  -p, --proc            enable process stats
  -r, --io              enable io stats (I/O requests completed)
  -s, --swap            enable swap stats
     -S swap1,total        include swap1 and total
  -t, --time            enable time/date output
  --time-adv            enable time/date output (with milliseconds)
  -T, --epoch           enable time counter (seconds since epoch)
  --epoch-adv           enable time counter (milliseconds since epoch)
  -y, --sys             enable system stats

  --aio                 enable aio stats
  --fs, --filesystem    enable fs stats
  --ipc                 enable ipcstats
  --lock                enable lockstats
  --raw                 enable rawstats
  --socket              enable socketstats
  --tcp                 enable tcpstats
  --udp                 enable udpstats
  --unix                enable unixstats
  --vm                  enable vmstats
  --vm-adv              enable advanced vm stats

  --list                list all available plugins
  --plugin              enable external plugin by name, see --list

  -a, --all             equals -cdngy (default)
  -f, --full            automatically expand -C, -D, -I, -N and -S lists
  -v, --vmstat          equals -pmgdsc -D total

  --bits                force bits for values expressed in bytes
  --float               force float values on screen
  --integer             force integer values on screen

  --bw, --blackonwhite  change colors for white background terminal
  --color               force colors
  --nocolor             disable colors
  --noheaders           disable repetitive headers
  --noupdate            disable intermediate updates
  --nomissed            disable missed ticks warnings
  -o file, --output=file
                        write CSV output to file

delay is the delay in seconds between each update (default: 1)
count is the number of updates to display before exiting (default: unlimited)
```
输出内容：
```
➜  ~ dstat
You did not select any stats, using -cdngy by default.
----total-usage---- -dsk/total- -net/total- ---paging-- ---system--
usr sys idl wai stl| read  writ| recv  send|  in   out | int   csw 
  0   0 100   0   0|   0   100k| 271B  282B|   0     0 | 583   699 
  0   0  99   0   0|   0   104k|   0     0 |   0     0 | 480   540 
  0   0 100   0   0|   0     0 |  66B   94B|   0     0 | 415   482 
  0   0  99   0   0|   0  4096B|   0     0 |   0     0 | 350   406 
  0   0 100   0   0|   0     0 |   0     0 |   0     0 | 338   388 ^C
```
也可以输出csv格式的文件。  

## sar
sar也是一个实时工具，能输出更为全面详细的信息，sar从/proc文件系统中收集信息，输出日志文件在/var/log/saDD中(DD是系统的日期)。使用sar时需要先配置并启用sysstat服务。sar也是sysstat软件包的一部分。  
```
➜  ~ sar --help
Usage: sar [ options ] [ <interval> [ <count> ] ]
Main options and reports (report name between square brackets):
	-B	Paging statistics [A_PAGE]
	-b	I/O and transfer rate statistics [A_IO]
	-d	Block devices statistics [A_DISK]
	-F [ MOUNT ]
		Filesystems statistics [A_FS]
	-H	Hugepages utilization statistics [A_HUGE]
	-I [ SUM | ALL ]
		Interrupts statistics [A_IRQ]
	-m { <keyword> [,...] | ALL }
		Power management statistics [A_PWR_...]
		Keywords are:
		BAT	Batteries capacity
		CPU	CPU instantaneous clock frequency
		FAN	Fans speed
		FREQ	CPU average clock frequency
		IN	Voltage inputs
		TEMP	Devices temperature
		USB	USB devices plugged into the system
	-n { <keyword> [,...] | ALL }
		Network statistics [A_NET_...]
		Keywords are:
		DEV	Network interfaces
		EDEV	Network interfaces (errors)
		NFS	NFS client
		NFSD	NFS server
		SOCK	Sockets	(v4)
		IP	IP traffic	(v4)
		EIP	IP traffic	(v4) (errors)
		ICMP	ICMP traffic	(v4)
		EICMP	ICMP traffic	(v4) (errors)
		TCP	TCP traffic	(v4)
		ETCP	TCP traffic	(v4) (errors)
		UDP	UDP traffic	(v4)
		SOCK6	Sockets	(v6)
		IP6	IP traffic	(v6)
		EIP6	IP traffic	(v6) (errors)
		ICMP6	ICMP traffic	(v6)
		EICMP6	ICMP traffic	(v6) (errors)
		UDP6	UDP traffic	(v6)
		FC	Fibre channel HBAs
		SOFT	Software-based network processing
	-q [ <keyword> [,...] | PSI | ALL ]
		System load and pressure-stall statistics
		Keywords are:
		LOAD	Queue length and load average statistics [A_QUEUE]
		CPU	Pressure-stall CPU statistics [A_PSI_CPU]
		IO	Pressure-stall I/O statistics [A_PSI_IO]
		MEM	Pressure-stall memory statistics [A_PSI_MEM]
	-r [ ALL ]
		Memory utilization statistics [A_MEMORY]
	-S	Swap space utilization statistics [A_MEMORY]
	-u [ ALL ]
		CPU utilization statistics [A_CPU]
	-v	Kernel tables statistics [A_KTABLES]
	-W	Swapping statistics [A_SWAP]
	-w	Task creation and system switching statistics [A_PCSW]
	-y	TTY devices statistics [A_SERIAL]
```
 `sar  2 10`就是2s内采样10次，`sar 1 10 -P ALL`可以每秒采样10次ALL个cpu核心的使用状态：  
```
 ➜  ~ sar 1 10 -P ALL  
Linux 6.10.4-200.fc40.x86_64 (bogon) 	08/18/2024 	_x86_64_	(8 CPU)

05:27:40 PM     CPU     %user     %nice   %system   %iowait    %steal     %idle
05:27:41 PM     all      0.12      0.00      0.12      0.38      0.00     99.38
05:27:41 PM       0      1.00      0.00      0.00      0.00      0.00     99.00
05:27:41 PM       1      0.00      0.00      0.00      2.00      0.00     98.00
05:27:41 PM       2      0.00      0.00      0.00      1.00      0.00     99.00
05:27:41 PM       3      0.00      0.00      0.00      0.00      0.00    100.00
05:27:41 PM       4      0.00      0.00      0.00      0.00      0.00    100.00
05:27:41 PM       5      0.00      0.00      0.00      0.00      0.00    100.00
05:27:41 PM       6      0.00      0.00      0.00      0.00      0.00    100.00
05:27:41 PM       7      0.00      0.00      1.00      0.00      0.00     99.00
^C

Average:        CPU     %user     %nice   %system   %iowait    %steal     %idle
Average:        all      0.12      0.00      0.12      0.38      0.00     99.38
Average:          0      1.00      0.00      0.00      0.00      0.00     99.00
Average:          1      0.00      0.00      0.00      2.00      0.00     98.00
Average:          2      0.00      0.00      0.00      1.00      0.00     99.00
Average:          3      0.00      0.00      0.00      0.00      0.00    100.00
Average:          4      0.00      0.00      0.00      0.00      0.00    100.00
Average:          5      0.00      0.00      0.00      0.00      0.00    100.00
Average:          6      0.00      0.00      0.00      0.00      0.00    100.00
Average:          7      0.00      0.00      1.00      0.00      0.00     99.00

```
最后会统计出平均值,  比如`%iowait`,显示了cpu等待IO请求的空闲时间，如果这个值长期高于0,则说明磁盘或者网络存在瓶颈。`%idle`则说明了cpu空闲状态，如果长期为0，则说明cpu在满负荷运载。（我觉得htop更直观）。   
然后是内存可以用`sar -r `  
```
➜  ~ sar -r 1 10 
Linux 6.10.4-200.fc40.x86_64 (bogon) 	08/18/2024 	_x86_64_	(8 CPU)

05:39:46 PM kbmemfree   kbavail kbmemused  %memused kbbuffers  kbcached  kbcommit   %commit  kbactive   kbinact   kbdirty
05:39:47 PM   7964424  11464048   3740680     23.03      5500   4188456  19655328     79.80   5100952   2184304      1388
05:39:48 PM   7965356  11464980   3735900     23.00      5500   4192304  19659424     79.81   5100768   2184304      1404
05:39:49 PM   7985484  11485112   3732856     22.98      5500   4175200  19642320     79.74   5100608   2184308       776
^C
Average:      7971755  11471380   3736479     23.00      5500   4185320  19652357     79.78   5100776   2184305      1189
```
然后是分页情况可以用`sar -B`,  
```
➜  ~ sar 3 5 -B 
Linux 6.10.4-200.fc40.x86_64 (bogon) 	08/18/2024 	_x86_64_	(8 CPU)

05:41:03 PM  pgpgin/s pgpgout/s   fault/s  majflt/s  pgfree/s pgscank/s pgscand/s pgsteal/s  pgprom/s   pgdem/s
05:41:06 PM      0.00    233.33    404.33      0.00   2260.33      0.00      0.00      0.00      0.00      0.00
05:41:09 PM      0.00      1.33    460.00      0.00   1507.33      0.00      0.00      0.00      0.00      0.00
^C
Average:         0.00    117.33    432.17      0.00   1883.83      0.00      0.00      0.00      0.00      0.00

```
`majft/s`展示了每一秒的主要页错误，这个很常见，在程序加载请求内存某处的数据时，如果无法找到该页则会向内核发送一个缺页请求，然后内核会试图在磁盘内寻找该页并复制。
同样的对于设备读取可以使用`sar -d `选项  
```
➜  ~ sar -d 1 10 -p 
Linux 6.10.4-200.fc40.x86_64 (bogon) 	08/18/2024 	_x86_64_	(8 CPU)

05:45:35 PM       tps     rkB/s     wkB/s     dkB/s   areq-sz    aqu-sz     await     %util DEV
05:45:36 PM     26.00      0.00    280.00      0.00     10.77      0.04      1.23      2.90 nvme0n1
05:45:36 PM      0.00      0.00      0.00      0.00      0.00      0.00      0.00      0.00 zram0

05:45:36 PM       tps     rkB/s     wkB/s     dkB/s   areq-sz    aqu-sz     await     %util DEV
05:45:37 PM      0.00      0.00      0.00      0.00      0.00      0.00      0.00      0.00 nvme0n1
05:45:37 PM      0.00      0.00      0.00      0.00      0.00      0.00      0.00      0.00 zram0

05:45:37 PM       tps     rkB/s     wkB/s     dkB/s   areq-sz    aqu-sz     await     %util DEV
05:45:38 PM      0.00      0.00      0.00      0.00      0.00      0.00      0.00      0.00 nvme0n1
05:45:38 PM      0.00      0.00      0.00      0.00      0.00      0.00      0.00      0.00 zram0
^C

Average:          tps     rkB/s     wkB/s     dkB/s   areq-sz    aqu-sz     await     %util DEV
Average:         8.67      0.00     93.33      0.00     10.77      0.01      1.23      0.97 nvme0n1
Average:         0.00      0.00      0.00      0.00      0.00      0.00      0.00      0.00 zram0

```
 对于网络状态监测可以查看`sar -n [keyword]`,我们可以加入对应的[keyword]来查看相应内容
* DEV: Generates a statistic report for all network devices
* EDEV: Generates an error statistics report for all network devices
* NFS: Generates a statistic report for an NFS client
* NFSD: Generates a statistic report for an NFS server
* SOCK: Generates a statistic report on sockets
* ALL: Generates all network statistic reports  

## top和htop
top：  
```
➜  ~ top --help 

Usage:
 top [options]

Options:
 -b, --batch-mode                run in non-interactive batch mode
 -c, --cmdline-toggle            reverse last remembered 'c' state
 -d, --delay =SECS [.TENTHS]     iterative delay as SECS [.TENTHS]
 -E, --scale-summary-mem =SCALE  set mem as: k,m,g,t,p,e for SCALE
 -e, --scale-task-mem =SCALE     set mem with: k,m,g,t,p for SCALE
 -H, --threads-show              show tasks plus all their threads
 -i, --idle-toggle               reverse last remembered 'i' state
 -n, --iterations =NUMBER        exit on maximum iterations NUMBER
 -O, --list-fields               output all field names, then exit
 -o, --sort-override =FIELD      force sorting on this named FIELD
 -p, --pid =PIDLIST              monitor only the tasks in PIDLIST
 -S, --accum-time-toggle         reverse last remembered 'S' state
 -s, --secure-mode               run with secure mode restrictions
 -U, --filter-any-user =USER     show only processes owned by USER
 -u, --filter-only-euser =USER   show only processes owned by USER
 -w, --width [=COLUMNS]          change print width [,use COLUMNS]
 -1, --single-cpu-toggle         reverse last remembered '1' state

 -h, --help                      display this help text, then exit
 -V, --version                   output version information & exit

```
对于top输出的信息：    
```
top - 17:57:31 up 19:35,  1 user,  load average: 0.50, 0.35, 0.32 # 系统时间，登陆用户，系统负载：最近1min，5min，15min
Tasks: 331 total,   1 running, 330 sleeping,   0 stopped,   0 zombie #进程状态
%Cpu(s):  6.4 us,  0.6 sy,  0.0 ni, 92.6 id,  0.1 wa,  0.1 hi,  0.2 si,  0.0 st  # 内存状态
MiB Mem :  15862.5 total,   7572.7 free,   4868.6 used,   4280.7 buff/cache     # 内存使用
MiB Swap:   8192.0 total,   8192.0 free,      0.0 used.  10993.9 avail Mem 		# 交换空间状态

```

在top内按h可以进入help：  
```
Help for Interactive Commands - procps-ng 4.0.4
Window 1:Def: Cumulative mode Off.  System: Delay 3.0 secs; Secure mode Off.

  Z,B,E,e   Global: 'Z' colors; 'B' bold; 'E'/'e' summary/task memory scale
  l,t,m,I,0 Toggle: 'l' load avg; 't' task/cpu; 'm' memory; 'I' Irix; '0' zeros
  1,2,3,4,5 Toggle: '1/2/3' cpu/numa views; '4' cpus abreast; '5' P/E-cores
  f,X       Fields: 'f' add/remove/order/sort; 'X' increase fixed-width fields

  L,&,<,> . Locate: 'L'/'&' find/again; Move sort column: '<'/'>' left/right
  R,H,J,C . Toggle: 'R' Sort; 'H' Threads; 'J' Num justify; 'C' Coordinates
  c,i,S,j . Toggle: 'c' Cmd name/line; 'i' Idle; 'S' Time; 'j' Str justify
  x,y     . Toggle highlights: 'x' sort field; 'y' running tasks
  z,b     . Toggle: 'z' color/mono; 'b' bold/reverse (only if 'x' or 'y')
  u,U,o,O . Filter by: 'u'/'U' effective/any user; 'o'/'O' other criteria
  n,#,^O  . Set: 'n'/'#' max tasks displayed; Show: Ctrl+'O' other filter(s)
  V,v,F   . Toggle: 'V' forest view; 'v' hide/show children; 'F' keep focused

  d,k,r,^R 'd' set delay; 'k' kill; 'r' renice; Ctrl+'R' renice autogroup
  ^G,K,N,U  View: ctl groups ^G; cmdline ^K; environment ^N; supp groups ^U
  Y,!,^E,P  Inspect 'Y'; Combine Cpus '!'; Scale time ^E; View namespaces ^P
  W,q       Write config file 'W'; Quit 'q'
          ( commands shown with '.' require a visible task display window ) 
Press 'h' or '?' for help with Windows,

```
htop会更现代一些：  
```
➜  ~ htop  --help
htop 3.3.0
(C) 2004-2019 Hisham Muhammad. (C) 2020-2024 htop dev team.
Released under the GNU GPLv2+.

-C --no-color                   Use a monochrome color scheme
-d --delay=DELAY                Set the delay between updates, in tenths of seconds
-F --filter=FILTER              Show only the commands matching the given filter
-h --help                       Print this help screen
-H --highlight-changes[=DELAY]  Highlight new and old processes
-M --no-mouse                   Disable the mouse
-n --max-iterations=NUMBER      Exit htop after NUMBER iterations/frame updates
-p --pid=PID[,PID,PID...]       Show only the given PIDs
   --readonly                   Disable all system and process changing features
-s --sort-key=COLUMN            Sort by COLUMN in list view (try --sort-key=help for a list)
-t --tree                       Show the tree view (can be combined with -s)
-u --user[=USERNAME]            Show only processes for a given user (or $USER)
-U --no-unicode                 Do not use unicode but plain ASCII
-V --version                    Print version info
   --drop-capabilities[=off|basic|strict] Drop Linux capabilities when running as root
                                off - do not drop any capabilities
                                basic (default) - drop all capabilities not needed by htop
                                strict - drop all capabilities except those needed for
                                         core functionality

```

# System Information  
这方面的工具可就太多了，列举几个常用的吧。  
##  进程运行相关的  
`pidstat`也是sysstat的成员，
```
PIDSTAT(1)                                                                Linux User's Manual                                                                PIDSTAT(1)

NAME
       pidstat - Report statistics for Linux tasks.

SYNOPSIS
       pidstat [ -d ] [ -H ] [ -h ] [ -I ] [ -l ] [ -R ] [ -r ] [ -s ] [ -t ] [ -U [ username ] ] [ -u ] [ -V ] [ -v ] [ -w ] [ -C comm ] [ -G process_name ] [ --dec={
       0 | 1 | 2 } ] [ --human ] [ -p { pid[,...]  | SELF | ALL } ] [ -T { TASK | CHILD | ALL } ] [ interval [ count ] ] [ -e program args ]

DESCRIPTION
	pidstat用于监视被kernel管理的独立的进程，-T输出进程树，-l查看进程的所带参数,-d选择特定项展示
	
```
还有一般系统自带命令`ps`  
```
EXAMPLES
       To see every process on the system using standard syntax:
          ps -e
          ps -ef
          ps -eF
          ps -ely

       To see every process on the system using BSD syntax:
          ps ax
          ps axu

       To print a process tree:
          ps -ejH
          ps axjf

       To get info about threads:
          ps -eLf
          ps axms

       To get security info:
          ps -eo euser,ruser,suser,fuser,f,comm,label
          ps axZ
          ps -eM

       To see every process running as root (real & effective ID) in user format:
          ps -U root -u root u

       To see every process with a user-defined format:
          ps -eo pid,tid,class,rtprio,ni,pri,psr,pcpu,stat,wchan:14,comm
          ps axo stat,euid,ruid,tty,tpgid,sess,pgrp,ppid,pid,pcpu,comm
          ps -Ao pid,tt,user,fname,tmout,f,wchan

       Print only the process IDs of syslogd:
          ps -C syslogd -o pid=

       Print only the name of PID 42:
          ps -q 42 -o comm=
```
 要具体分析某一个进程的所有参数，使用的环境变量等，我们可以查看`/proc/[pid]`的文件内容。比如chrome的进程详情：
 ```
 24038   20648  0  80   0 - 8617004 do_sys 18:26 ?      00:00:38 /opt/google/chrome/chrome
 ```
 ```
 ➜  24038 pwd
/proc/24038
➜  24038 ls
arch_status  clear_refs          cpuset   fdinfo             latency    mem         ns             pagemap      sched      smaps_rollup  syscall         uid_map
attr         cmdline             cwd      gid_map            limits     mountinfo   numa_maps      patch_state  schedstat  stack         task            wchan
autogroup    comm                environ  io                 loginuid   mounts      oom_adj        personality  sessionid  stat          timens_offsets
auxv         coredump_filter     exe      ksm_merging_pages  map_files  mountstats  oom_score      projid_map   setgroups  statm         timers
cgroup       cpu_resctrl_groups  fd       ksm_stat           maps       net         oom_score_adj  root         smaps      status        timerslack_ns
```
进程的资源限制  
```
➜  24038 cat limits 
Limit                     Soft Limit           Hard Limit           Units     
Max cpu time              unlimited            unlimited            seconds   
Max file size             unlimited            unlimited            bytes     
Max data size             unlimited            unlimited            bytes     
Max stack size            8388608              unlimited            bytes     
Max core file size        unlimited            unlimited            bytes     
Max resident set          unlimited            unlimited            bytes     
Max processes             63276                63276                processes 
Max open files            8192                 524288               files     
Max locked memory         8388608              8388608              bytes     
Max address space         unlimited            unlimited            bytes     
Max file locks            unlimited            unlimited            locks     
Max pending signals       63276                63276                signals   
Max msgqueue size         819200               819200               bytes     
Max nice priority         0                    0                    
Max realtime priority     0                    0                    
Max realtime timeout      200000               200000               us 
```
进程的内存映射  
```

.......
55c9d36bd000-55c9d6073000 r--p 00000000 00:21 313220                     /opt/google/chrome/chrome
55c9d6074000-55c9e15cb000 r-xp 029b6000 00:21 313220                     /opt/google/chrome/chrome
55c9e15cb000-55c9e1f3b000 r--p 0df0c000 00:21 313220                     /opt/google/chrome/chrome
55c9e1f3c000-55c9e1fdb000 rw-p 0e87c000 00:21 313220                     /opt/google/chrome/chrome
55c9e1fdb000-55c9e2216000 rw-p 00000000 00:00 0 
7f67e89fd000-7f67e8dfe000 rw-s 00000000 00:19 31189                      /dev/shm/.com.google.Chrome.2paG6K (deleted)
7f67e91ff000-7f67e93ff000 rw-s 00000000 00:19 25205                      /dev/shm/.com.google.Chrome.85mbcU (deleted)
7f67e97ff000-7f67e9c00000 rw-s 00000000 00:19 31628                      /dev/shm/.com.google.Chrome.KMf8KM (deleted)
7f67e9c00000-7f67e9e00000 rw-s 00000000 00:19 31123                      /dev/shm/.com.google.Chrome.TQYc44 (deleted)
7f67e9e00000-7f67ea000000 rw-s 00000000 00:19 28885                      /dev/shm/.com.google.Chrome.a3yoDs (deleted)
7f67ea000000-7f67ea001000 ---p 00000000 00:00 0 
7f67ea001000-7f67ea801000 rw-p 00000000 00:00 0 
7f67eaa00000-7f67eac00000 rw-s 00000000 00:19 27993                      /dev/shm/.com.google.Chrome.lLMoFU (deleted)
7f67eac00000-7f67eae00000 rw-s 00000000 00:19 28881                      /dev/shm/.com.google.Chrome.F0dyOY (deleted)
7f67eae00000-7f67eb000000 rw-s 00000000 00:19 30033                      /dev/shm/.com.google.Chrome.1Voh8V (deleted)
7f67eb000000-7f67eb254000 r--p 00000000 00:21 66406                      /usr/share/fonts/gdouros-symbola/Symbola.ttf
7f67eb400000-7f67eb401000 ---p 00000000 00:00 0 
7f67eb401000-7f67ebc01000 rw-p 00000000 00:00 0 
7f67ebe00000-7f67ec000000 rw-s 00000000 00:19 26544                      /dev/shm/.com.google.Chrome.09l3Mp (deleted)
7f67ec000000-7f67ec200000 rw-s 00000000 00:19 24984                      /dev/shm/.com.google.Chrome.jybCNY (deleted)
7f67ec200000-7f67ec201000 ---p 00000000 00:00 0 
7f67ec201000-7f67eca01000 rw-p 00000000 00:00 0 
7f67ecc00000-7f67ecc01000 ---p 00000000 00:00 0 
7f67ecc01000-7f67ed401000 rw-p 00000000 00:00 0 
7f67ed600000-7f67ed601000 ---p 00000000 00:00 0 
7f67ed601000-7f67ede01000 rw-p 00000000 00:00 0 
7f67ee000000-7f67ee001000 ---p 00000000 00:00 0 
7f67ee001000-7f67ee801000 rw-p 00000000 00:00 0 
7f67eea00000-7f67eea01000 ---p 00000000 00:00 0 
7f67eea01000-7f67ef201000 rw-p 00000000 00:00 0 
7f67ef400000-7f67ef7d9000 r--p 00000000 00:21 66449                      /usr/share/fonts/google-droid-sans-fonts/DroidSansFallbackFull.ttf

.......
```
 进程使用的系统当前挂载信息  
 ```
 ➜  24038 cat mountinfo 
66 1 0:33 /root / rw,relatime shared:1 - btrfs /dev/nvme0n1p3 rw,seclabel,compress=zstd:1,ssd,discard=async,space_cache=v2,subvolid=257,subvol=/root
35 66 0:6 / /dev rw,nosuid shared:2 - devtmpfs devtmpfs rw,seclabel,size=4096k,nr_inodes=2024848,mode=755,inode64
36 35 0:25 / /dev/shm rw,nosuid,nodev shared:3 - tmpfs tmpfs rw,seclabel,inode64
37 35 0:26 / /dev/pts rw,nosuid,noexec,relatime shared:4 - devpts devpts rw,seclabel,gid=5,mode=620,ptmxmode=000
38 66 0:24 / /sys rw,nosuid,nodev,noexec,relatime shared:5 - sysfs sysfs rw,seclabel
39 38 0:7 / /sys/kernel/security rw,nosuid,nodev,noexec,relatime shared:6 - securityfs securityfs rw
40 38 0:28 / /sys/fs/cgroup rw,nosuid,nodev,noexec,relatime shared:7 - cgroup2 cgroup2 rw,seclabel,nsdelegate,memory_recursiveprot
41 38 0:29 / /sys/fs/pstore rw,nosuid,nodev,noexec,relatime shared:8 - pstore pstore rw,seclabel
42 38 0:30 / /sys/firmware/efi/efivars rw,nosuid,nodev,noexec,relatime shared:9 - efivarfs efivarfs rw
43 38 0:31 / /sys/fs/bpf rw,nosuid,nodev,noexec,relatime shared:10 - bpf bpf rw,mode=700
44 38 0:32 / /sys/kernel/config rw,nosuid,nodev,noexec,relatime shared:11 - configfs configfs rw
45 66 0:23 / /proc rw,nosuid,nodev,noexec,relatime shared:13 - proc proc rw
46 66 0:27 / /run rw,nosuid,nodev shared:14 - tmpfs tmpfs rw,seclabel,size=3248648k,nr_inodes=819200,mode=755,inode64
25 38 0:21 / /sys/fs/selinux rw,nosuid,noexec,relatime shared:12 - selinuxfs selinuxfs rw
24 45 0:36 / /proc/sys/fs/binfmt_misc rw,relatime shared:15 - autofs systemd-1 rw,fd=37,pgrp=1,timeout=0,minproto=5,maxproto=5,direct,pipe_ino=6661
27 35 0:37 / /dev/hugepages rw,nosuid,nodev,relatime shared:16 - hugetlbfs hugetlbfs rw,seclabel,pagesize=2M
28 35 0:20 / /dev/mqueue rw,nosuid,nodev,noexec,relatime shared:17 - mqueue mqueue rw,seclabel
31 38 0:8 / /sys/kernel/debug rw,nosuid,nodev,noexec,relatime shared:18 - debugfs none rw,seclabel
32 38 0:13 / /sys/kernel/tracing rw,nosuid,nodev,noexec,relatime shared:19 - tracefs tracefs rw,seclabel
33 38 0:38 / /sys/fs/fuse/connections rw,nosuid,nodev,noexec,relatime shared:20 - fusectl fusectl rw
48 66 0:33 /home /home rw,relatime shared:73 - btrfs /dev/nvme0n1p3 rw,seclabel,compress=zstd:1,ssd,discard=async,space_cache=v2,subvolid=256,subvol=/home
51 66 0:42 / /tmp rw,nosuid,nodev shared:76 - tmpfs tmpfs rw,seclabel,nr_inodes=1048576,inode64
54 66 259:2 / /boot rw,relatime shared:79 - ext4 /dev/nvme0n1p2 rw,seclabel
57 54 259:1 / /boot/efi rw,relatime shared:82 - vfat /dev/nvme0n1p1 rw,fmask=0077,dmask=0077,codepage=437,iocharset=ascii,shortname=winnt,errors=remount-ro
60 24 0:43 / /proc/sys/fs/binfmt_misc rw,nosuid,nodev,noexec,relatime shared:85 - binfmt_misc binfmt_misc rw
143 66 0:50 / /var/lib/nfs/rpc_pipefs rw,relatime shared:174 - rpc_pipefs sunrpc rw
26 46 0:82 / /run/user/1000 rw,nosuid,nodev,relatime shared:1041 - tmpfs tmpfs rw,seclabel,size=1624320k,nr_inodes=406080,mode=700,uid=1000,gid=1000,inode64
318 26 0:83 / /run/user/1000/gvfs rw,nosuid,nodev,relatime shared:1045 - fuse.gvfsd-fuse gvfsd-fuse rw,user_id=1000,group_id=1000
1203 26 0:84 / /run/user/1000/doc rw,nosuid,nodev,relatime shared:1127 - fuse.portal portal rw,user_id=1000,group_id=1000

```

##  设备状态信息
可以使用sysstat里的iostat  
```
IOSTAT(1)                                                                 Linux User's Manual                                                                 IOSTAT(1)

NAME
       iostat - Report Central Processing Unit (CPU) statistics and input/output statistics for devices and partitions.

SYNOPSIS
       iostat  [ -c ] [ -d ] [ -h ] [ -k | -m ] [ -N ] [ -s ] [ -t ] [ -V ] [ -x ] [ -y ] [ -z ] [ --compact ] [ --dec={ 0 | 1 | 2 } ] [ { -f | +f } directory ] [ -j {
       ID | LABEL | PATH | UUID | ... } ] [ -o JSON ] [ [ -H ] -g group_name ] [ --human ] [ --pretty ] [ -p [ device[,...] | ALL ] ] [ device [...] | ALL ] [ interval
       [ count ] ]

DESCRIPTION
		iostat通过观察io设备在它们使用时间内的数据传输效率，
```
输出  
```
➜  ~ iostat -x 
Linux 6.10.4-200.fc40.x86_64 (fedora)   08/18/2024      _x86_64_        (8 CPU)

avg-cpu:  %user   %nice %system %iowait  %steal   %idle
           1.76    0.01    0.74    0.26    0.00   97.23

Device            r/s     rkB/s   rrqm/s  %rrqm r_await rareq-sz     w/s     wkB/s   wrqm/s  %wrqm w_await wareq-sz     d/s     dkB/s   drqm/s  %drqm d_await dareq-sz     f/s f_await  aqu-sz  %util
nvme0n1          0.54     23.88     0.08  12.56    1.00    44.36    8.19    123.21     0.18   2.15    2.55    15.05    0.03    145.84     0.00   0.00    1.72  5216.03    0.29    3.29    0.02   0.29
zram0            0.00      0.01     0.00   0.00    0.00    21.85    0.00      0.00     0.00   0.00    0.00     4.00    0.00      0.00     0.00   0.00    0.00     0.00    0.00    0.00    0.00   0.00

```

也可以在一定时间内采集指定次数的数据。
然后还有`/dev`目录下，使用的字符设备和块设备会在此处创建设备节点文件。/dev主要是为系统提供设备的访问节点，而/sys文件系统比/dev要更新，且携带了设备的更多信息。比如cpu  
```
➜  /sys cat ./devices/system/cpu/cpufreq/policy0/scaling_cur_freq 
3601102
➜  /sys cat ./devices/system/cpu/cpufreq/policy7/cpuinfo_transition_latency 
0
➜  /sys cat ./devices/system/cpu/cpufreq/policy7/cpuinfo_max_freq
3600000
➜  /sys 

```
内存 
```
➜  /sys cat ./devices/system/memory/block_size_bytes 
8000000
➜  /sys cat ./devices/system/memory/auto_online_blocks 
online

```
网卡  
```
➜  /sys cat  ./class/net/enp0s31f6/mtu 
1500
➜  /sys cat  ./class/net/enp0s31f6/speed 
-1
```
当然了，也可以直接往/sys系统的设备文件内写值来配置设备.  

##  查看日志
查看内核`dmesg`  
其他日志`journalctl`  

## 网络相关 
查看当前所有设备、ip地址可以使用iproute2包的内容：`ip a` 
```
➜  ~ ip a
1: lo: <LOOPBACK,UP,LOWER_UP> mtu 65536 qdisc noqueue state UNKNOWN group default qlen 1000
    link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
    inet 127.0.0.1/8 scope host lo
       valid_lft forever preferred_lft forever
    inet6 ::1/128 scope host noprefixroute 
       valid_lft forever preferred_lft forever
2: enp0s31f6: <NO-CARRIER,BROADCAST,MULTICAST,UP> mtu 1500 qdisc fq_codel state DOWN group default qlen 1000
    link/ether c8:f7:50:68:cc:ac brd ff:ff:ff:ff:ff:ff  
```
  
一般比较新的系统会使用Networkmanager管理网络，所以可以使用`nmcli `相关指令  
然后是查看当前系统网络监听的TCP/UDP端口  `netstat -tulp`  
```
➜  ~ netstat -tulp
(Not all processes could be identified, non-owned process info
 will not be shown, you would have to be root to see it all.)
Active Internet connections (only servers)
Proto Recv-Q Send-Q Local Address           Foreign Address         State       PID/Program name    
tcp        0      0 0.0.0.0:llmnr           0.0.0.0:*               LISTEN      -                   
tcp        0      0 _localdnsstub:domain    0.0.0.0:*               LISTEN      -                   
tcp        0      0 _localdnsproxy:domain   0.0.0.0:*               LISTEN      -                   
tcp        0      0 localhost:33211         0.0.0.0:*               LISTEN      -                   

```
 也可以使用更新的`ss 	-tulpn`  
 ```
 Netid      State       Recv-Q      Send-Q                                Local Address:Port            Peer Address:Port      Process                                       
udp        UNCONN      0           0                                       224.0.0.251:5353                 0.0.0.0:*          users:(("chrome",pid=24038,fd=185))          
udp        UNCONN      0           0                                       224.0.0.251:5353                 0.0.0.0:*          users:(("chrome",pid=24038,fd=120))          
udp        UNCONN      0           0                                           0.0.0.0:5353                 0.0.0.0:*                                                       
udp        UNCONN      0           0                                           0.0.0.0:5355                 0.0.0.0:*                                                       
udp        UNCONN      0           0                                        127.0.0.54:53                   0.0.0.0:*                                                       
udp        UNCONN      0           0                                     127.0.0.53%lo:53                   0.0.0.0:*                                                       
udp        UNCONN      0           0                                         127.0.0.1:323                  0.0.0.0:*                                                       
udp        UNCONN      0           0                                           0.0.0.0:44214                0.0.0.0:*                                                       
udp        UNCONN      0           0                                              [::]:5353                    [::]:*                                                       
udp        UNCONN      0           0                                              [::]:5355                    [::]:*                                                       
```
 
 还有`lsof`,列出所有打开的文件   
 ```
 ➜  ~ lsof -i
COMMAND     PID      USER   FD   TYPE DEVICE SIZE/OFF NODE NAME
gnome-she 20648 jlibyvali   62u  IPv4 371625      0t0  TCP localhost:41902->localhost:7897 (ESTABLISHED)
```

还有手动的，可以查看`/proc/net`下和`/sys/class/net/`下的文件信息  
``` 
➜  net pwd
/proc/net
➜  net ls
anycast6   dev_snmp6     if_inet6       ip6_tables_matches  ip_tables_names    netfilter            protocols  route      snmp          tcp       unix
arp        fib_trie      igmp           ip6_tables_names    ip_tables_targets  netlink              psched     rpc        snmp6         tcp6      wireless
bnep       fib_triestat  igmp6          ip6_tables_targets  ipv6_route         netstat              ptype      rt6_stats  sockstat      udp       xfrm_stat
connector  hci           ip6_flowlabel  ip_mr_cache         l2cap              nf_conntrack         raw        rt_acct    sockstat6     udp6
dev        icmp          ip6_mr_cache   ip_mr_vif           mcfilter           nf_conntrack_expect  raw6       rt_cache   softnet_stat  udplite
dev_mcast  icmp6         ip6_mr_vif     ip_tables_matches   mcfilter6          packet               rfcomm     sco        stat          udplite6

➜  net pwd
/sys/class/net
➜  net ls 
enp0s31f6  lo  wlp2s0

```
dns相关的`nslookup`和`dig` ,测试ssl连接的`openssl client`    
dns配置常用的有systemd-resolved,Networkmanager,dnsmasq。**推荐资料**:  
<https://wiki.archlinux.org/title/Systemd-resolved>
<https://www.freedesktop.org/software/systemd/man/latest/resolved.conf.html>
<https://wiki.archlinux.org/title/NetworkManager>
<https://wiki.archlinux.org/title/Dnsmasq>

感觉使用默认的systemd-resolved或者Networkmanager是比较简单的，当然注意有些代理软件会劫持dns，关闭代理后需要刷新本地dns服务器缓存。  
然后是防火墙的Debian/Ubuntu常用ufw。其他的firewall-cmd,还有手动的iptables
## 系统相关的服务,守护进程，uevent事件管理。  
对于有使用`systemd`的系统，可以方便的使用`systemctl  list-units --type=service` 查看所有服务  
``` 
➜  ~ systemctl list-units --type=service 
  UNIT                                                                                      LOAD   ACTIVE SUB     DESCRIPTION                                              >
  abrt-journal-core.service                                                                 loaded active running ABRT coredumpctl message creator
  abrt-oops.service                                                                         loaded active running ABRT kernel log watcher
  abrt-xorg.service                                                                         loaded active running ABRT Xorg log watcher
  abrtd.service                                                                             loaded active running ABRT Daemon
  accounts-daemon.service                                                                   loaded active running Accounts Service
  alsa-state.service                                                                        loaded active running Manage Sound Card State (restore and store)
  auditd.service                                                                            loaded active running Security Audit Logging Service

```
对于守护进程,一般以`d`结尾i，ps 加 grep筛选即可  
对于uevents事件监控，`sudo udeadmin monitor`,此时可以插入一个u盘查看事件。  
``` 
➜  ~ sudo udevadm monitor 
monitor will print the received events for:
UDEV - the event which udev sends out after rule processing
KERNEL - the kernel uevent

KERNEL[11523.441106] add      /devices/pci0000:00/0000:00:14.0/usb1/1-9 (usb)
KERNEL[11523.444999] add      /devices/pci0000:00/0000:00:14.0/usb1/1-9/1-9:1.0 (usb)
KERNEL[11523.445204] add      /devices/virtual/workqueue/scsi_tmf_0 (workqueue)
KERNEL[11523.445239] add      /devices/pci0000:00/0000:00:14.0/usb1/1-9/1-9:1.0/host0 (scsi)
KERNEL[11523.445263] add      /devices/pci0000:00/0000:00:14.0/usb1/1-9/1-9:1.0/host0/scsi_host/host0 (scsi_host)
KERNEL[11523.445299] bind     /devices/pci0000:00/0000:00:14.0/usb1/1-9/1-9:1.0 (usb)
KERNEL[11523.445337] bind     /devices/pci0000:00/0000:00:14.0/usb1/1-9 (usb)
UDEV  [11523.446523] add      /devices/virtual/workqueue/scsi_tmf_0 (workqueue)
UDEV  [11523.451793] add      /devices/pci0000:00/0000:00:14.0/usb1/1-9 (usb)
UDEV  [11523.453725] add      /devices/pci0000:00/0000:00:14.0/usb1/1-9/1-9:1.0 (usb)
UDEV  [11523.454829] add      /devices/pci0000:00/0000:00:14.0/usb1/1-9/1-9:1.0/host0 (scsi)
UDEV  [11523.455897] add      /devices/pci0000:00/0000:00:14.0/usb1/1-9/1-9:1.0/host0/scsi_host/host0 (scsi_host)
UDEV  [11523.456765] bind     /devices/pci0000:00/0000:00:14.0/usb1/1-9/1-9:1.0 (usb)
UDEV  [11523.460665] bind     /devices/pci0000:00/0000:00:14.0/usb1/1-9 (usb)
KERNEL[11524.783262] add      /devices/pci0000:00/0000:00:14.0/usb1/1-9/1-9:1.0/host0/target0:0:0 (scsi)
KERNEL[11524.783316] add      /devices/pci0000:00/0000:00:14.0/usb1/1-9/1-9:1.0/host0/target0:0:0/0:0:0:0 (scsi)
KERNEL[11524.783350] add      /devices/pci0000:00/0000:00:14.0/usb1/1-9/1-9:1.0/host0/target0:0:0/0:0:0:0/scsi_device/0:0:0:0 (scsi_device)
KERNEL[11524.783387] add      /devices/pci0000:00/0000:00:14.0/usb1/1-9/1-9:1.0/host0/target0:0:0/0:0:0:0/scsi_disk/0:0:0:0 (scsi_disk)
KERNEL[11524.783428] add      /devices/pci0000:00/0000:00:14.0/usb1/1-9/1-9:1.0/host0/target0:0:0/0:0:0:0/scsi_generic/sg0 (scsi_generic)
KERNEL[11524.783603] add      /devices/pci0000:00/0000:00:14.0/usb1/1-9/1-9:1.0/host0/target0:0:0/0:0:0:0/bsg/0:0:0:0 (bsg)
UDEV  [11524.785215] add      /devices/pci0000:00/0000:00:14.0/usb1/1-9/1-9:1.0/host0/target0:0:0 (scsi)
UDEV  [11524.786484] add      /devices/pci0000:00/0000:00:14.0/usb1/1-9/1-9:1.0/host0/target0:0:0/0:0:0:0 (scsi)
KERNEL[11524.792336] add      /devices/virtual/bdi/8:0 (bdi)
KERNEL[11524.803394] add      /devices/pci0000:00/0000:00:14.0/usb1/1-9/1-9:1.0/host0/target0:0:0/0:0:0:0/block/sda (block)
KERNEL[11524.803450] add      /devices/pci0000:00/0000:00:14.0/usb1/1-9/1-9:1.0/host0/target0:0:0/0:0:0:0/block/sda/sda1 (block)
KERNEL[11524.803483] bind     /devices/pci0000:00/0000:00:14.0/usb1/1-9/1-9:1.0/host0/target0:0:0/0:0:0:0 (scsi)
UDEV  [11524.804755] add      /devices/pci0000:00/0000:00:14.0/usb1/1-9/1-9:1.0/host0/target0:0:0/0:0:0:0/scsi_device/0:0:0:0 (scsi_device)
UDEV  [11524.804791] add      /devices/pci0000:00/0000:00:14.0/usb1/1-9/1-9:1.0/host0/target0:0:0/0:0:0:0/scsi_disk/0:0:0:0 (scsi_disk)
UDEV  [11524.806448] add      /devices/pci0000:00/0000:00:14.0/usb1/1-9/1-9:1.0/host0/target0:0:0/0:0:0:0/scsi_generic/sg0 (scsi_generic)
UDEV  [11524.807463] add      /devices/pci0000:00/0000:00:14.0/usb1/1-9/1-9:1.0/host0/target0:0:0/0:0:0:0/bsg/0:0:0:0 (bsg)
UDEV  [11524.809118] add      /devices/virtual/bdi/8:0 (bdi)
UDEV  [11524.931275] add      /devices/pci0000:00/0000:00:14.0/usb1/1-9/1-9:1.0/host0/target0:0:0/0:0:0:0/block/sda (block)

```
## 对于IPC资源的使用 
`ipcs`
``` 
ipcs
------ Message Queues --------
key        msqid      owner      perms      used-bytes   messages

------ Shared Memory Segments --------
key        shmid      owner      perms      bytes      nattch     status
0x00000000 65536      tux        600        524288     2          dest
0x00000000 98305      tux        600        4194304    2          dest
0x00000000 884738     root       600        524288     2          dest
0x00000000 786435     tux        600        4194304    2          dest
0x00000000 12058628   tux        600        524288     2          dest
0x00000000 917509     root       600        524288     2          dest
0x00000000 12353542   tux        600        196608     2          dest
0x00000000 12451847   tux        600        524288     2          dest
0x00000000 11567114   root       600        262144     1          dest
0x00000000 10911763   tux        600        2097152    2          dest
0x00000000 11665429   root       600        2336768    2          dest
0x00000000 11698198   root       600        196608     2          dest
0x00000000 11730967   root       600        524288     2          dest

------ Semaphore Arrays --------
key        semid      owner      perms      nsems
0xa12e0919 32768      tux        666        2
```
## lsxx系列
比如`lsblk`,`lsclocks`,`lscpu`,`lsmem`,`lsmdev`,`lsmod`等等，这些命令一般随系统自带，位于软件包`util-linux`中。 
```
➜  ~ lscpu
Architecture:             x86_64
  CPU op-mode(s):         32-bit, 64-bit
  Address sizes:          39 bits physical, 48 bits virtual
  Byte Order:             Little Endian
CPU(s):                   8
  On-line CPU(s) list:    0-7
Vendor ID:                GenuineIntel
  Model name:             Intel(R) Core(TM) i5-8350U CPU @ 1.70GHz
    CPU family:           6
    Model:                142
    Thread(s) per core:   2
    Core(s) per socket:   4
    Socket(s):            1
    Stepping:             10
    CPU(s) scaling MHz:   78%
    CPU max MHz:          3600.0000
    CPU min MHz:          400.0000
    BogoMIPS:             3799.90
    Flags:                fpu vme de pse tsc msr pae mce cx8 apic sep mtrr pge mca cmov pat pse36 clflush dts acpi mmx fxsr sse sse2 ss ht tm pbe syscall nx pdpe1gb rdtscp 
                          lm constant_tsc art arch_perfmon pebs bts rep_good nopl xtopology nonstop_tsc cpuid aperfmperf pni pclmulqdq dtes64 monitor ds_cpl vmx smx est tm2
                           ssse3 sdbg fma cx16 xtpr pdcm pcid sse4_1 sse4_2 x2apic movbe popcnt tsc_deadline_timer aes xsave avx f16c rdrand lahf_lm abm 3dnowprefetch cpuid
                          _fault epb pti ssbd ibrs ibpb stibp tpr_shadow flexpriority ept vpid ept_ad fsgsbase tsc_adjust bmi1 avx2 smep bmi2 erms invpcid mpx rdseed adx sm
                          ap clflushopt intel_pt xsaveopt xsavec xgetbv1 xsaves dtherm ida arat pln pts hwp hwp_notify hwp_act_window hwp_epp vnmi md_clear flush_l1d arch_c
                          apabilities
Virtualization features:  
  Virtualization:         VT-x
Caches (sum of all):      
  L1d:                    128 KiB (4 instances)
  L1i:                    128 KiB (4 instances)
  L2:                     1 MiB (4 instances)
  L3:                     6 MiB (1 instance)
NUMA:                     
  NUMA node(s):           1
  NUMA node0 CPU(s):      0-7
Vulnerabilities:          
  Gather data sampling:   Mitigation; Microcode
  Itlb multihit:          KVM: Mitigation: VMX disabled
  L1tf:                   Mitigation; PTE Inversion; VMX conditional cache flushes, SMT vulnerable
  Mds:                    Mitigation; Clear CPU buffers; SMT vulnerable
  Meltdown:               Mitigation; PTI
  Mmio stale data:        Mitigation; Clear CPU buffers; SMT vulnerable
  Reg file data sampling: Not affected
  Retbleed:               Mitigation; IBRS
  Spec rstack overflow:   Not affected
  Spec store bypass:      Mitigation; Speculative Store Bypass disabled via prctl
  Spectre v1:             Mitigation; usercopy/swapgs barriers and __user pointer sanitization
  Spectre v2:             Mitigation; IBRS; IBPB conditional; STIBP conditional; RSB filling; PBRSB-eIBRS Not affected; BHI Not affected
  Srbds:                  Mitigation; Microcode
  Tsx async abort:        Mitigation; TSX disabled

```
```
➜  ~ lspci
00:00.0 Host bridge: Intel Corporation Xeon E3-1200 v6/7th Gen Core Processor Host Bridge/DRAM Registers (rev 08)
00:02.0 VGA compatible controller: Intel Corporation UHD Graphics 620 (rev 07)
00:04.0 Signal processing controller: Intel Corporation Xeon E3-1200 v5/E3-1500 v5/6th Gen Core Processor Thermal Subsystem (rev 08)
00:14.0 USB controller: Intel Corporation Sunrise Point-LP USB 3.0 xHCI Controller (rev 21)
00:14.2 Signal processing controller: Intel Corporation Sunrise Point-LP Thermal subsystem (rev 21)
00:15.0 Signal processing controller: Intel Corporation Sunrise Point-LP Serial IO I2C Controller #0 (rev 21)
00:15.1 Signal processing controller: Intel Corporation Sunrise Point-LP Serial IO I2C Controller #1 (rev 21)
00:15.2 Signal processing controller: Intel Corporation Sunrise Point-LP Serial IO I2C Controller #2 (rev 21)
00:15.3 Signal processing controller: Intel Corporation Sunrise Point-LP Serial IO I2C Controller #3 (rev 21)
```

```
➜  ~ lsipc
RESOURCE DESCRIPTION                                              LIMIT USED  USE%
MSGMNI   Number of message queues                                 32000    0 0.00%
MSGMAX   Max size of message (bytes)                                 8K    -     -
MSGMNB   Default max size of queue (bytes)                          16K    -     -
SHMMNI   Shared memory segments                                    4096    0 0.00%
SHMALL   Shared memory pages                       18446744073692774399    0 0.00%
SHMMAX   Max size of shared memory segment (bytes)                  16E    -     -
SHMMIN   Min size of shared memory segment (bytes)                   1B    -     -
SEMMNI   Number of semaphore identifiers                          32000    0 0.00%
SEMMNS   Total number of semaphores                          1024000000    0 0.00%
SEMMSL   Max semaphores per semaphore set.                        32000    -     -
SEMOPM   Max number of operations per semop(2)                      500    -     -
SEMVMX   Semaphore max value                                      32767    -     -

```
```
➜  ~ lsirq
IRQ    TOTAL NAME
LOC 14256285 Local timer interrupts
 17  5705206 IR-IO-APIC 17-fasteoi i2c_designware.1, idma64.1
CAL   899517 Function call interrupts
140   817574 IR-PCI-MSI-0000:00:02.0 0-edge i915
IWI   473546 IRQ work interrupts
TLB   420519 TLB shootdowns
  9   406754 IR-IO-APIC 9-fasteoi acpi
142   396167 IR-PCI-MSI-0000:00:1f.6 0-edge enp0s31f6
 51   239162 IR-IO-APIC 51-fasteoi DELL081C:00
133    83699 IR-PCI-MSIX-0000:03:00.0 1-edge nvme0q1
RES    47677 Rescheduling interrupts
136    38973 IR-PCI-MSIX-0000:03:00.0 4-edge nvme0q4
134    37771 IR-PCI-MSIX-0000:03:00.0 2-edge nvme0q2
137    36049 IR-PCI-MSIX-0000:03:00.0 5-edge nvme0q5
139    32324 IR-PCI-MSIX-0000:03:00.0 7-edge nvme0q7
135    30714 IR-PCI-MSIX-0000:03:00.0 3-edge nvme0q3
138    26428 IR-PCI-MSIX-0000:03:00.0 6-edge nvme0q6
  1    11656 IR-IO-APIC 1-edge i8042

......
```
```
➜  ~ lsmem 
RANGE                                  SIZE  STATE REMOVABLE  BLOCK
0x0000000000000000-0x00000000cfffffff  3.3G online       yes   0-25
0x0000000100000000-0x000000042fffffff 12.8G online       yes 32-133

Memory block size:       128M
Total online memory:      16G
Total offline memory:      0B

```

```
➜  ~ lsmod 
Module                  Size  Used by
uas                    36864  0
usb_storage            90112  1 uas
rfcomm                102400  0
tun                    73728  0
uinput                 20480  0
snd_seq_dummy          12288  0
snd_hrtimer            12288  1
nf_conntrack_netbios_ns    12288  1
nf_conntrack_broadcast    12288  1 nf_conntrack_netbios_ns
nft_fib_inet           12288  1
nft_fib_ipv4           12288  1 nft_fib_inet
nft_fib_ipv6           12288  1 nft_fib_inet
nft_fib                12288  3 nft_fib_ipv6,nft_fib_ipv4,nft_fib_inet
nft_reject_inet        12288  10

```
##  /proc/sys  
/proc/sys也称为系统控制参数，用于修饰内核运行时的参数  
```
➜  ~ sysctl --help

Usage:
 sysctl [options] [variable[=value] ...]

Options:
  -a, --all            display all variables
  -A                   alias of -a
  -X                   alias of -a
      --deprecated     include deprecated parameters to listing
      --dry-run        Print the key and values but do not write
  -b, --binary         print value without new line
  -e, --ignore         ignore unknown variables errors
  -N, --names          print variable names without values
  -n, --values         print only values of the given variable(s)
  -p, --load[=<file>]  read values from file
  -f                   alias of -p
      --system         read values from all system directories
  -r, --pattern <expression>
                       select setting that match expression
  -q, --quiet          do not echo variable set
  -w, --write          enable writing a value to variable
  -o                   does nothing
  -x                   does nothing
  -d                   alias of -h

 -h, --help     display this help and exit
 -V, --version  output version information and exit

```
`sysctl -a`列出所有参数，sysctl将可以修改的参数分组，比如`sysctl fs`,`sysctl dev`,`sysctl kernel`,`sysctl net`,`sysctl vm`。 

## 文件与文件系统
文件系统最常用的`mount`,`df`,`du`,`fdisk`  
`mount`用于展示系统内所有文件系统的挂载信息，完成挂载相关操作。 
`df`用于展示主要文件系统的使用量。 
`du`主要用于计算大小。  
阅读文件相关的命令常用的：`stat`,`readelf`,分析文件内部的section和symbol的`objdump`,`nm`
`readelf`主要查看elf二进制格式文件信息，即使是不同架构的编译产物。  

``` 
readelf --file-header /bin/ls
ELF Header:
  Magic:   7f 45 4c 46 02 01 01 00 00 00 00 00 00 00 00 00
  Class:                             ELF64
  Data:                              2's complement, little endian
  Version:                           1 (current)
  OS/ABI:                            UNIX - System V
  ABI Version:                       0
  Type:                              EXEC (Executable file)
  Machine:                           Advanced Micro Devices X86-64
  Version:                           0x1
  Entry point address:               0x402540
  Start of program headers:          64 (bytes into file)
  Start of section headers:          95720 (bytes into file)
  Flags:                             0x0
  Size of this header:               64 (bytes)
  Size of program headers:           56 (bytes)
  Number of program headers:         9
  Size of section headers:           64 (bytes)
  Number of section headers:         32
  Section header string table index: 31
```
`stat`主要展示文件属性  
```
➜  ~ stat ./.zshrc 
  File: ./.zshrc
  Size: 4648            Blocks: 16         IO Block: 4096   regular file
Device: 0,41    Inode: 53136       Links: 1
Access: (0644/-rw-r--r--)  Uid: ( 1000/jlibyvali)   Gid: ( 1000/jlibyvali)
Context: unconfined_u:object_r:user_home_t:s0
Access: 2024-08-18 18:19:30.430482095 +0800
Modify: 2024-08-18 18:18:21.339954059 +0800
Change: 2024-08-18 18:18:21.352953970 +0800
 Birth: 2024-08-18 18:18:21.339954059 +0800

```
 
`objdump`主要用于分析文件的节，头部表信息：  

```
 ➜  ~ objdump -h /usr/bin/objdump 

/usr/bin/objdump:     file format elf64-x86-64

Sections:
Idx Name          Size      VMA               LMA               File off  Algn
  0 .interp       0000001c  0000000000000318  0000000000000318  00000318  2**0
                  CONTENTS, ALLOC, LOAD, READONLY, DATA
  1 .note.gnu.property 00000050  0000000000000338  0000000000000338  00000338  2**3
                  CONTENTS, ALLOC, LOAD, READONLY, DATA
  2 .note.gnu.build-id 00000024  0000000000000388  0000000000000388  00000388  2**2
                  CONTENTS, ALLOC, LOAD, READONLY, DATA
  3 .note.ABI-tag 00000020  00000000000003ac  00000000000003ac  000003ac  2**2
                  CONTENTS, ALLOC, LOAD, READONLY, DATA
  4 .note.package 0000008c  00000000000003cc  00000000000003cc  000003cc  2**2
                  CONTENTS, ALLOC, LOAD, READONLY, DATA
  5 .gnu.hash     00000048  0000000000000458  0000000000000458  00000458  2**3
                  CONTENTS, ALLOC, LOAD, READONLY, DATA
  6 .dynsym       00001050  00000000000004a0  00000000000004a0  000004a0  2**3
                  CONTENTS, ALLOC, LOAD, READONLY, DATA
  7 .dynstr       00000a00  00000000000014f0  00000000000014f0  000014f0  2**0
                  CONTENTS, ALLOC, LOAD, READONLY, DATA
  8 .gnu.version  0000015c  0000000000001ef0  0000000000001ef0  00001ef0  2**1
                  CONTENTS, ALLOC, LOAD, READONLY, DATA
  9 .gnu.version_r 00000120  0000000000002050  0000000000002050  00002050  2**3
                  CONTENTS, ALLOC, LOAD, READONLY, DATA
 10 .rela.dyn     00000150  0000000000002170  0000000000002170  00002170  2**3
                  CONTENTS, ALLOC, LOAD, READONLY, DATA
 11 .rela.plt     00000ee8  00000000000022c0  00000000000022c0  000022c0  2**3
                  CONTENTS, ALLOC, LOAD, READONLY, DATA
 12 .relr.dyn     00000118  00000000000031a8  00000000000031a8  000031a8  2**3
                  CONTENTS, ALLOC, LOAD, READONLY, DATA
 13 .init         0000001b  0000000000004000  0000000000004000  00004000  2**2
                  CONTENTS, ALLOC, LOAD, READONLY, CODE
 14 .plt          00000a00  0000000000004020  0000000000004020  00004020  2**4
                  CONTENTS, ALLOC, LOAD, READONLY, CODE
 15 .plt.sec      000009f0  0000000000004a20  0000000000004a20  00004a20  2**4
                  CONTENTS, ALLOC, LOAD, READONLY, CODE
 16 .text         00046424  0000000000005410  0000000000005410  00005410  2**4
                  CONTENTS, ALLOC, LOAD, READONLY, CODE
 17 .fini         0000000d  000000000004b834  000000000004b834  0004b834  2**2
                  CONTENTS, ALLOC, LOAD, READONLY, CODE
 18 .rodata       000152d3  000000000004c000  000000000004c000  0004c000  2**5
                  CONTENTS, ALLOC, LOAD, READONLY, DATA
 19 .eh_frame_hdr 000008e4  00000000000612d4  00000000000612d4  000612d4  2**2
                  CONTENTS, ALLOC, LOAD, READONLY, DATA
 20 .eh_frame     0000307c  0000000000061bb8  0000000000061bb8  00061bb8  2**3
                  CONTENTS, ALLOC, LOAD, READONLY, DATA
 21 .init_array   00000008  00000000000652b0  00000000000652b0  000652b0  2**3
                  CONTENTS, ALLOC, LOAD, DATA
 22 .fini_array   00000008  00000000000652b8  00000000000652b8  000652b8  2**3
                  CONTENTS, ALLOC, LOAD, DATA
 23 .data.rel.ro  00002580  00000000000652c0  00000000000652c0  000652c0  2**5
                  CONTENTS, ALLOC, LOAD, DATA
 24 .dynamic      00000270  0000000000067840  0000000000067840  00067840  2**3
                  CONTENTS, ALLOC, LOAD, DATA
 25 .got          00000548  0000000000067ab0  0000000000067ab0  00067ab0  2**3
                  CONTENTS, ALLOC, LOAD, DATA
 26 .data         00001d90  0000000000068000  0000000000068000  00068000  2**5
                  CONTENTS, ALLOC, LOAD, DATA
 27 .bss          00002e48  0000000000069da0  0000000000069da0  00069d90  2**5
                  ALLOC
 28 .gnu.build.attributes 00000048  000000000006ebe8  000000000006ebe8  00069d90  2**2
                  CONTENTS, READONLY, OCTETS
 29 .gnu_debuglink 00000028  0000000000000000  0000000000000000  00069dd8  2**2
                  CONTENTS, READONLY
 30 .gnu_debugdata 0000119c  0000000000000000  0000000000000000  00069e00  2**0
                  CONTENTS, READONLY
```
也可以用于反汇编，查看节的具体内容等。  
`nm`主要用于分析文件的符号表信息，当然这得要求程序在编译时保留了符号信息，不然是没有的。 

---
# User Information    


`fuser`,能检测是哪个用户的或进程正在占用该文件，比如要`umount /mnt/*`时显示`xxx is busy`，使用fuser可以查看是谁在使用它。  
```
fuser -v /mnt/*

                     USER        PID ACCESS COMMAND
/mnt/notes.txt       tux    26597 f....  less
```

## 时间地区其它
`timedatactl`可以显示现在的时区，控制日期时间。  
`localectl`用于控制当前的区域，语言设置。 
`hostnamectl`用于显示，设置当前机器的主机名字。  
还有用户程序相关的配置文件，程序数据存放路径有关的`XDG env`系列： 
**<https://specifications.freedesktop.org/basedir-spec/latest>**  


> 内容文字较多，难免遗漏、出错，欢迎斧正  
 
#  参考资料
* <https://doc.opensuse.org/documentation/leap/tuning/html/book-tuning/cha-util.html>
* <https://doc.opensuse.org/documentation/leap/tuning/html/book-tuning/cha-tuning-syslog.html>
* <https://doc.opensuse.org/documentation/leap/tuning/html/book-tuning/part-tuning-kerneltrace.html>
