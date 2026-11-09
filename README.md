## Repair Bad Tweaks

<img width="288" height="256" alt="{62D32C78-7D2B-4902-ABEB-F42DAC55E0DD}" src="https://github.com/user-attachments/assets/ce5431c4-fb89-42aa-a34c-1341e150002d" />



### Why?
As tweaking windows becomes more popular self proclaimed experts incorrectly interpret microsoft documentation and other sources. This causes less tech savvy people to blindly trust these "tweakers" and negatively effect the performance of their operating system. This script aims to revert these tweaks to their default values. 

### How to Use?
#### Option 1: 
Run Script From Terminal or PowerShell Prompt as Admin
````
 irm 'https://raw.githubusercontent.com/iFryno/RepairBadTweaks/main/RepairTweaks.ps1' | iex 
 ````

 #### Option 2:
 Download Script and Run Locally 

 **Note:** Unblock PowerShell Scripts on Windows 11 before running to avoid it closing instantly

 ```Set-executionpolicy Unrestricted -Force```

### Tweaks Explained
- **Jump to a Tweak:**

    - [Svc Split Threshold](#svc-split-threshold)
    
    - [Bcdedit](#bcdedit)
    - [Timer Resolution](#timer-resolution)
    - [Win32PrioritySeparation](#win32priorityseparation)
    - [Tcp Auto-Tuning](#tcp-auto-tuning)
    - [Prefetch and Superfetch (Sysmain)](#prefetch-and-superfetch-sysmain)
    - [Windows Error Reporting](#windows-error-reporting)
    - [Ordinary DPCs](#ordinary-dpcs)
    - [Spectre Meltdown Mitigations](#spectre-meltdown-mitigations)
    - [HPET](#hpet)
    - [Mouse Keyboard Queue Size](#mouse-keyboard-queue-size)
    - [Csrss Priority](#Csrss-Priority)
    - [Multi-Plane Overlay](#Multi-Plane-Overlay)
    - [Memory Management](#Memory-Management)
    - [Raw Mouse Throttle](#Raw-Mouse-Throttle)
    - [Global Timer Resolution Requests](#Global-Timer-Resolution-Requests)



### Svc Split Threshold
- This tweak is completely visual in task manager and does not actually decrease processes. Microsoft recommends having a separate service host for certain services to increase system stability and resource management [Read More](https://learn.microsoft.com/en-us/windows/application-management/svchost-service-refactoring)


### Bcdedit
 - Values Checked and Repaired: 
    - useplatformclock
    - disabledynamictick
    - useplatformtick
    - tscsyncpolicy

These values are not set by default and should only be used for debugging (recommended by microsoft). These are typically changed in relation to timer res (read more below).
### Timer Resolution
 - Possibly the most widely misunderstood part of windows as this tweak is "believed" by many to decrease input lag and increase fps. Unfortunately timer resolution has nothing to do with either of these things. Operating system timers are used to sync events and keep track of how long a process took to complete. This is essential in online gaming where things are not in real time rather predicted. Many apps will set timer resolution to 1ms, however this is simply a resolution not a set value. Some people have noticed that this value is not always exactly the same, this is by design [Read More](https://stackoverflow.com/questions/3744032/why-are-net-timers-limited-to-15-ms-resolution)


 **NOTE** Windows does not use just 1 timer to keep track of events there are many timers you can see them with cpuz, this is also why professional benchmarks are measured using a number of these to get the most accurate metrics. Additionally, for very fast/accurate events a performance counter is likely to be used [Read More](https://learn.microsoft.com/en-us/windows/win32/api/profileapi/nf-profileapi-queryperformancecounter)


### Win32PrioritySeparation
 - This registry value tells the windows scheduler how much cpu time a process should get. While there is lots of options outside of the two "**Adjust for best performance: programs or background services**" in legacy control panel, the default value when using "Programs" gives the scheduler the ideal cpu time for boosting foreground apps (longer cpu time).
 **NOTE This will not affect input lag in anyway as I/O are received as interrupts and take cpu priority**

### Tcp Auto-Tuning
 - Auto tuning allows the system to dynamically adjust the receive window for incoming data using TCP [Transmission Control Protocol] [Read More](https://learn.microsoft.com/en-us/troubleshoot/windows-server/networking/receive-window-auto-tuning-for-http#how-receive-window-auto-tuning-feature-improves-data-transfer). For online gaming we do not care about this protocol as the more important events are being sent using UDP [User Datagram Protocol]. This also means the popular tcp optimizer program is completely useless for improving your network related to gaming.


### Prefetch and Superfetch (Sysmain)
 - Prefetch in Windows caches necessary data for apps instead of retrieving it from memory every time the program is launched. This helps apps to start up faster with no negative system performance drawbacks (some disk space...).


 - Superfetch is very similar to prefetch however it is able to change its caching parameters based on your frequently used apps. This is important because why would you need to cache an app you only used once. Disabling either of these will only slow your operating system down and increase loading times of apps.

### Windows Error Reporting
 - For some reason some tweak guides will disable windows error reporting. If this service is disabled no mini dump file will be made when getting a BSOD. So good luck reading about the error code with your preferred bluescreen viewer. 

### Ordinary DPCs
 - DPC [Deferred Procedure Call] tells the windows scheduler to defer a lower priority task for an interrupt (typically I/O). Starting in Vista Microsoft added threaded dpcs to decrease system latency when lots of dpcs are on the system. For some reason people think using ordinary dpcs helps input lag while Microsoft explicitly recommend using threaded [Read More](https://learn.microsoft.com/en-us/windows-hardware/drivers/kernel/introduction-to-threaded-dpcs)

### Spectre Meltdown Mitigations
- Disabling these mitigations does not improve gaming performance and only leaves your system vulnerable to very dangerous exploits. You can find many benchmarks online showing that disabling these does not help however I always recommend doing your own with a fps analytics tool such as CapFrameX.

### HPET
 - Luckily this tweak is quite "legacy" however some people still disable HPET in device manager. This is a hardware timer similar to performance counter and is able to get extremely precise events. This was likely thought to help latency in the past because of the decrease latency reading in latencymon. HPET can produce interrupts thus when disabling latencymon will show a lower reading [Read More](https://en.wikipedia.org/wiki/High_Precision_Event_Timer).

### Mouse Keyboard Queue Size
 - These registry values decide how many bytes are allocated to the non-paged pool for mice and keyboards by default mice get 24 bytes and keyboards get 12 bytes. Each time an interrupt from these devices is received bytes are "freed" dynamically based on the amount of data. Changing these from the default values will have no effect on mouse and keyboard latency.

### Csrss Priority
- Csrss.exe is a critical system process responsible for things like loading assemblies. For some reason a tweak has gone around to change the priority class of this process. Changing the priority class for any process is a bad idea but especially csrss.exe since its a critical process meaning a crash will cause a BSOD to occur. Learn More about why changing priorities is a bad idea [Here](https://learn.microsoft.com/en-us/windows/win32/procthread/scheduling-priorities#priority-class)


### Multi-Plane Overlay
- Multi-Plane Overlay or MPO allows for multiple overlay planes to exist separate from each other but appear as one image to the user. This feature allows for dwm and other overlays to not add a latency penalty. This along with FSO should NOT be disabled as it is actually beneficial for input lag. Refer to the image below:
![alt text](image.png)

### Memory Management
- While theres lots of registry keys that are located in the Memory Management location none of them should be touched. Most keys are only for 32bit systems or have not been used since XP, Vista days. If you want to try out a properly setup memory management configuration that is different from default windows builds try a server build such as server 2025. 

**Read More:**

https://www.tweakhound.com/2011/09/20/bad-tweaks 

https://serverfault.com/questions/12150/does-the-disablepagingexecutive-registry-change-have-any-actual-effect

https://learn.microsoft.com/en-us/windows/win32/memory/memory-management-registry-keys

> [!NOTE]  
> If the script detects this bad tweak it will attempt to repair it to default, however I recommend a fresh install
>

### Raw Mouse Throttle

- Back in May of 2023 Microsoft added a new change to Windows 11 that mitigated game stutters when using a high polling rate mouse (Ex. 8k hz Polling) 
- This change made it so all background listeners would be throttled and coalesced to a default value of 125hz
- This is a good change as it means background apps that are listening to raw mouse input would not be nearly as resource intensive as before 

#### Additional Note

- the amount of throttle can be adjusted or removed completley(NOT RECOMMENDED)
- By changing the duration registry key the throttle can be lowered down to 50hz, you can try this out and see if you feel an improvement on desktop
  
```ps
reg add "HKCU\Control Panel\Mouse" /v "RawMouseThrottleEnabled" /t REG_DWORD /d "1" /f
reg add "HKCU\Control Panel\Mouse" /v "RawMouseThrottleDuration" /t REG_DWORD /d "20" /f
```
> [!NOTE]
> Setting this to 50hz will not effect games, there could be a rare case of a user with an extremely slow cpu and 8k hz mouse where this could be potentially useful
> 


#### Dev Blog Documenting This Change
https://blogs.windows.com/windowsdeveloper/2023/05/26/delivering-delightful-performance-for-more-than-one-billion-users-worldwide/


### Global Timer Resolution Requests
 - This registry key added in Windows 11 and later versions of Windows 10 overrides the updated behavior of global timer requests
 - Before this registry key was added the lowest timer reqested by a process would reflect to all other processes so Fe. if app1 is requesting a timer resolution of 15ms but app2 requests 1ms then app1 will also be changed to 1ms along with all other processes. 
- This behavoir was changed to be per process, mitigating uncessary context switching of processes thus slightly improving performance 

> [!NOTE]
> This also means without applying this registry key all timer resolution apps will not do anything but change their own timer resolution
>

