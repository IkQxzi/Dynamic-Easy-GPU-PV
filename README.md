# Dynamic-Easy-GPU-PV
An improvement on [Easy-GPU-PV](https://github.com/jamesstringerparsec/Easy-GPU-PV) which allows you to create a virtual machine and dynamically share a GPU with it. The VM will take up as much or as little GPU as it needs (takes precedence over the host), which is a better method of GPU partitioning than Easy-GPU-PV, which divides the GPU by a fixed percentage, as it is very useful if the host and VM need to use different amounts of GPU resources.

GPU-PV allows you to partition your system's dedicated or integrated GPU and assign it to several Hyper-V VMs.  It's the same technology that is used in WSL2, and Windows Sandbox.  

Dynamic-Easy-GPU-PV aims to make this easier by automating the steps required to get a GPU-PV VM up and running.  
Dynamic-Easy-GPU-PV does the following...  
1) Creates a VM of your choosing
2) Automatically Installs Windows to the VM
3) Dynamically partitions your GPU of choice and copies the required driver files to the VM  
4) Installs [Parsec](https://parsec.app) to the VM, Parsec is an ultra-low latency remote desktop app, use this to connect to the VM.  You can use Parsec for free non commercially. To use Parsec commercially, sign up to a [Parsec For Teams](https://parsec.app/teams) account  

### Prerequisites:
* Windows 10 20H1+ Pro, Enterprise or Education OR Windows 11 Pro, Enterprise or Education.  Windows 11 on host and VM is preferred due to better compatibility.  
* Matched Windows versions between the host and VM. Mismatches may cause compatibility issues, blue-screens, or other issues. (Win10 21H1 + Win10 21H1, or Win11 21H2 + Win11 21H2, for example)  
* Desktop Computer with dedicated NVIDIA/AMD GPU or Integrated Intel GPU - Laptops with NVIDIA GPUs are not supported by Parsec this time, but Intel integrated GPUs work on laptops with Parsec. (NVIDIA Laptop GPUs and other GPUs without hardware encoding can still be partitioned but must be connected to using alternatives like [Steam Link](https://steamcommunity.com/groups/homestream/discussions/0/540732889255066292/?l=french#:~:text=An%20easier%20way,press%20Alt%2BF4.) [max. 1080p30fps] {achieved by adding Wordpad to the VM's library then logging into the same account on another client} or [RDP](https://support.microsoft.com/en-us/windows/how-to-use-remote-desktop-5fe128d5-8fb1-7a23-3b8a-41e636865e8c) [RDP only allows 30fps connections to and from Windows PCs on the same network])
* GPU must support hardware video encoding ([NVIDIA NVENC](https://developer.nvidia.com/video-encode-and-decode-gpu-support-matrix-new), [Intel Quicksync](https://www.intel.com/content/www/us/en/support/articles/000029338/graphics.html) or AMD AMF).  
* Latest GPU driver from Intel.com or NVIDIA.com, don't rely on Device Manager or Windows update.  
* Latest Windows 10 ISO [downloaded from here](https://www.microsoft.com/en-gb/software-download/windows10ISO) / Windows 11 ISO [downloaded from here.](https://www.microsoft.com/en-us/software-download/windows11) - Do not use Media Creation Tool, if no direct ISO link is available, follow [this guide](https://www.nextofwindows.com/downloading-windows-10-iso-images-using-rufus) - [using Rufus 3.5.](https://github.com/pbatard/rufus/releases/download/v3.5/rufus-3.5.exe)
* Virtualisation enabled in the motherboard and [Hyper-V fully enabled](https://docs.microsoft.com/en-us/virtualization/hyper-v-on-windows/quick-start/enable-hyper-v) on the Windows 10/ 11 OS (requires reboot).  
* Allow Powershell scripts to run on your system - typically by running "Set-ExecutionPolicy unrestricted" in Powershell running as Administrator.  
* In order to use networking on the Virtual Machine, (required for Parsec) [create a virtual switch.](https://learn.microsoft.com/en-us/windows-server/virtualization/hyper-v/get-started/create-a-virtual-switch-for-hyper-v-virtual-machines?tabs=hyper-v-manager)


### Instructions
1. Make sure your system meets the prerequisites.
2. [Download the Repo and extract.](https://github.com/IkQxzi/Dynamic-Easy-GPU-PV/archive/refs/tags/Release.zip)
3. Search your system for Powershell ISE and run as Administrator.
4. In the extracted folder you downloaded, open PreChecks.ps1 in Powershell ISE.  Run the files from within the extracted folder. Do not move them.
5. Open and Run PreChecks.ps1 in Powershell ISE using the green play button and copy the GPU Listed (or the warnings that you need to fix).
6. Open CopyFilesToVM.ps1 Powershell ISE and edit the params section at the top of the file, you need to be careful about how much RAM, storage, and hard drive space you give it as your system needs to have that available.  On Windows 10 the GPUName must be left as "AUTO", In Windows 11 it can be either "AUTO" or the specific name of the GPU you want to partition exactly how it appears in PreChecks.ps1.  Additionally, you need to provide the path to the Windows 10/11 ISO file you downloaded (refer to Prerequisites for instructions).
7. Run CopyFilesToVM.ps1 with your changes to the params section - this may take 5-10 minutes.
8. Open and sign into Parsec on the VM.  You can use Parsec to connect to the VM up to 4K60FPS.
* **DO NOT SKIP THE NEXT STEP OTHERWISE THE VM WILL TAKE UP ALL YOUR GPU RESOURCES**
9. Download [Rivatuner Statistics server](https://ftp.nluug.nl/pub/games/PC/guru3d/afterburner/[Guru3D.com]-RTSS.zip) (also comes packaged with [MSI Afterburner](https://download.msi.com/uti_exe/vga/MSIAfterburnerSetup.zip?__token__=exp=1695535772~acl=/*~hmac=264a5dd8a3e37aa07ac8a6a8ebe11a6f488bdf7189585ee70aa181d6b7edbb0e)) and [set the global framerate cap to 60](https://youtu.be/aa6ulv8wBsE). (It may need to be capped lower on slower computers).
10. You should be good to go!

### Upgrading GPU Drivers when you update the host GPU Drivers
It's important to update the VM GPU Drivers after you have updated the Host GPU's drivers. You can do this by...  
1. Reboot the host after updating GPU Drivers.  
2. Open Powershell as administrator and change the directory (CD) to the path where CopyFilesToVM.ps1 and Update-VMGpuPartitionDriver.ps1 are located. 
3. Run ```Update-VMGpuPartitionDriver.ps1 -VMName "Name of your VM" -GPUName "Name of your GPU"```    (Windows 10 GPU name must be "AUTO")

### Values
  ```VMName = "GPUP"``` - Name of VM in Hyper-V and the computername / hostname  
  ```SourcePath = "C:\Users\james\Downloads\Win11_English_x64.iso"``` - path to Windows 10/ 11 ISO on your host   
  ```Edition    = 6``` - Leave as 6, this means Windows 10/11 Pro  
  ```VhdFormat  = "VHDX"``` - Leave this value alone  
  ```DiskLayout = "UEFI"``` - Leave this value alone  
  ```SizeBytes  = 40GB``` - Disk size, in this case, 40GB - the minimum is 20GB (Large amounts of disk space recommended as the VM only uses as much disk space as it needs)
  ```MemoryAmount = 8GB``` - Memory size, in this case 8GB  
  ```CPUCores = 4``` - CPU Threads (not cores) you want to give VM, in this case, 4   
  ```NetworkSwitch = "Default Switch"``` - Leave this alone unless you're not using the default Hyper-V Switch  
  ```VHDPath = "C:\Users\Public\Documents\Hyper-V\Virtual Hard Disks\"``` - Path to the folder you want the VM Disk to be stored in, it must already exist  
  ```UnattendPath = "$PSScriptRoot"+"\autounattend.xml"``` -Leave this value alone  
  ```GPUName = "AUTO"``` - AUTO selects the first available GPU. On Windows 11 you may also use the exact name of the GPU you want to share with the VM in multi-GPU situations (GPU selection is not available in Windows 10 and must be set to AUTO)    
  ```Team_ID = ""``` - The Parsec for Teams ID if you are a Parsec for Teams Subscriber  
  ```Key = ""``` - The Parsec for Teams Secret Key if you are a Parsec for Teams Subscriber  
  ```Username = "GPUVM"``` - The VM Windows Username, do not include special characters, and must be different from the "VMName" value you set  
  ```Password = "CoolestPassword!"``` - The VM Windows Password, cannot be blank    
  ```Autologon = "true"```- If you want the VM to automatically login to the Windows Desktop


### Thanks to:  
- [Hyper-ConvertImage](https://github.com/tabs-not-spaces/Hyper-ConvertImage) for creating an updated version of [Convert-WindowsImage](https://github.com/MicrosoftDocs/Virtualization-Documentation/tree/master/hyperv-tools/Convert-WindowsImage) that is compatible with Windows 10 and 11.
- [gawainXX](https://github.com/gawainXX) for help testing and pointing out bugs and feature improvements.  


### Notes:    
- After you have signed into Parsec on the VM, always use Parsec to connect to the VM.  Keep the Microsft Hyper-V Video adapter disabled. Using RDP and Hyper-V Enhanced Session mode will result in broken behaviour and black screens in Parsec.  RDP and the Hyper-V video adapter only offer a maximum of 30FPS. Using Parsec will allow you to use up to 4k60 FPS.
- If you get "ERROR  : Cannot bind argument to parameter 'Path' because it is null." this probably means you used Media Creation Tool to download the ISO.  You unfortunately cannot use that, if you don't see a direct ISO download link on the Microsoft page, follow [this guide.](https://www.nextofwindows.com/downloading-windows-10-iso-images-using-rufus)  
- Your GPU on the host will have a Microsoft driver in Device Manager, rather than an Nvidia/Intel/AMD driver. As long as it doesn't have a yellow triangle over top of the device in Device Manager, it's working correctly.  
- A powered on display / HDMI dummy dongle must be plugged into the GPU to allow Parsec to capture the screen.  You only need one of these per host machine regardless of number of VM's.
- If your computer is super fast it may get to the login screen before the audio driver (VB Cable) and Parsec display driver are installed, but fear not! They should soon install.  
- The screen may go black for times up to 10 seconds in situations when UAC prompts appear, applications go in and out of fullscreen and when you switch between video codecs in Parsec - not really sure why this happens, it's unique to GPU-P machines and seems to recover faster at 1280x720.
- Vulkan renderer is unavailable and GL games may or may not work.  [This](https://www.microsoft.com/en-us/p/opencl-and-opengl-compatibility-pack/9nqpsl29bfff?SilentAuth=1&wa=wsignin1.0#activetab=pivot:overviewtab) may help with some OpenGL apps.  
- If you do not have administrator permissions on the machine it means you set the username and vmname to the same thing, these need to be different.  
- AMD Polaris GPUs like the RX 580 do not support hardware video encoding via GPU Paravirtualization at this time.  
- To download Windows ISOs with Rufus, it must have "Check for updates" enabled.
