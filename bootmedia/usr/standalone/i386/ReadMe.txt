

------------------------------------------------------------------------

                            Installation
                        ====================
 
  Normal Install (non-RAID)(Install Mac to first-disk)(boot0, boot0hfs):
------------------------------------------------------------------------
  List the partitions of root disk
            diskutil list
  /dev/disk0
   #:                       TYPE NAME            SIZE       IDENTIFIER
   0:     FDisk_partition_scheme		*320.0 GB   disk0
   1:               Windows_NTFS	WinXP	  30.0 GB   disk0s1
   2:               Apple_HFS		MacSnow	  40.0 GB   disk0s2
   3:               Windows_NTFS	Win7	  50.0 GB   disk0s3
   4:               Windows_NTFS	Data	 200.0 GB   disk0s5

  Suppose that your installation is on /dev/disk0s2 
   - Install boot0hfs to the MBR:
            sudo ./fdisk440 -f boot0hfs -u -y /dev/rdisk0
   - Install boot1h to the partition's bootsector:
            sudo dd if=boot1h of=/dev/rdisk0s2
   - Install boot to the partition's root directory:
            sudo cp boot /                                  Finish.
  

  Normal Install (non-RAID)(Install Mac to second-disk)(boot0md):
------------------------------------------------------------------------
  List the partitions of root disk
            diskutil list
  /dev/disk0
   #:                       TYPE NAME            SIZE       IDENTIFIER
   0:     FDisk_partition_scheme		*320.0 GB   disk0
   1:               Windows_NTFS	WinXP	  40.0 GB   disk0s1
   2:               Windows_NTFS	Win7	  60.0 GB   disk0s2
   3:               Windows_NTFS	Data	 220.0 GB   disk0s3
  /dev/disk1
   #:                       TYPE NAME            SIZE       IDENTIFIER
   0:     FDisk_partition_scheme		*500.0 GB   disk1
   1:               Windows_NTFS	Temp	 150.0 GB   disk1s1
   2:               Apple_HFS		MacSnow	  50.0 GB   disk1s2
   3:               Windows_NTFS	data	 300.0 GB   disk1s3

  Suppose that your installation is on /dev/disk1s2 
   - Install boot0md to the MBR of first-disk(boot disk):
            sudo ./fdisk440 -f boot0md -u -y /dev/rdisk0
   - Install boot1h to the partition's bootsector of second-disk:
            sudo dd if=boot1h of=/dev/rdisk1s2
   - Install boot to the partition's root directory:
            sudo cp boot /                                  Finish.

 boot0		searches for boot1h on the first active partition. 
		Need to activates your selected target partition.
 boot0hfs	searches for boot1h on the first partition, regardless 
		of active flag.
 boot0md	could search for boot1h on the first active partition
		of second disk. Need to activates the target partition.

Rename to be /Extra/com.apple.Boot.plist by yourself for Rev 1104 before only.
Rename to be /Extra/org.chameleon.Boot.plist by yourself for Rev 1105 after only.

------------------------------------------------------------------------


README


------------------------------------------------------------------------

                        Chameleon Boot Loader
                        =====================

  What is it?
  -----------

  Chameleon is combination of various boot loader components. It is based
  on David Elliott's fake EFI implementation added to Apple's boot-132
  project. Chameleon is extended with the following key features:
  

  Features
  --------
  
   - Device Property Injection via device-properties string in org.chameleon.Boot.plist
   - hybrid boot0+boot1h loaders for both MBR and GPT partitioned disks.
   - automatic FSB detection code even for recent AMD CPUs.
   - Apple Software RAID support.
   - stage2 loader (boot) can be placed as a regular file in the boot
     partition's root folder.
   - Modules
     

                        Installation
                        ============
  
  Normal Install (non-RAID):
  --------------------------
  
  Suppose that your installation is on /dev/disk0s2
  
   - Install boot0 to the MBR:
   		sudo ./fdisk440 -f boot0 -u -y /dev/rdisk0
  
   - Install boot1h to the partition's bootsector:
  		sudo dd if=boot1h of=/dev/rdisk0s2
  
   - Install boot to the partition's root directory:
  		sudo cp boot /
  
  No need to use startupfiletool anymore!
  
  
  RAID Install:
  -------------
  
  Suppose that your installation is on /dev/disk3, which is either a mirror- or a
  stripeset consisting of /dev/disk0 and /dev/disk1

  Mac OS X creates a small helper partition at the end of each RAID member disk,
  namely /dev/disk0s3 and /dev/disk1s3
  
   - Install boot0 to the MBR of both disks:
  		sudo ./fdisk440 -f boot0 -u -y /dev/rdisk0
  		sudo ./fdisk440 -f boot0 -u -y /dev/rdisk1
  
   - Install boot1h to the bootsector of each boot partition:
  		sudo dd if=boot1h of=/dev/rdisk0s3
  		sudo dd if=boot1h of=/dev/rdisk1s3
  
   - Install boot to both helper partition's root directories.
  		diskutil mount disk0s3
  		cp boot /Volumes/Boot\ OSX
  		diskutil unmount disk0s3
  		diskutil mount disk1s3
  		cp boot /Volumes/Boot\ OSX
  		diskutil unmount disk1s3

  Support:
  --------
  
  If you have any questions, issues etc. feel free to join us
  at irc.voodooprojects.org #chameleon or http://forum.voodooprojects.org/
  

  Source Code
  -----------

  For downloading the source code please visit the project page at
  http://forge.voodooprojects.org/p/chameleon/


  Licensing
  ---------

  Chameleon is released under the terms and conditions of
  Apple Public Source License (see APPLE_LICENSE).

------------------------------------------------------------------------


BootHelp.txt


------------------------------------------------------------------------

The boot: prompt waits for you to type advanced startup options.
If you don't type anything, the computer continues starting up normally. It
uses the kernel and configuration files on the startup device, which it also
uses as the root device.

Advanced startup options use the following syntax:

    [device]<kernel> [arguments]

Example arguments include

 device: rd=<BSD device name>       (e.g. rd=disk0s2)
         rd=*<IODeviceTree path>    (e.g. rd=*/PCI0@0/CHN0@0/@0:1)

 kernel: kernel name                (e.g. "mach_kernel" - must be in "/" )

 flags: -v (verbose),                -s (single user mode),
        -x (safe mode),              -f (ignore caches),
        -F (ignore "Kernel Flags" specified in boot configuration file)

 "Graphics Mode"="WIDTHxHEIGHTxDEPTH" (e.g. "1024x768x32")

 kernel flags                       (e.g. debug=0x144)
 io=0xffffffff                      (defined in IOKit/IOKitDebug.h)

Example: mach_kernel rd=disk0s1 -v "Graphics Mode"="1920x1200x32"

If the computer won't start up properly, you may be able to start it up using
safe mode.  Type -x to start up in safe mode, which ignores all cached
driver files.

Special booter hotkeys:
-----------------------
  F5            Rescans optical drive.
  F10           Scans and displays all BIOS accessible drives.

Special booter commands:
------------------------
  ?memory       Displays information about the computer's memory.
  ?video        Displays VESA video modes supported by the computer's BIOS.
  ?norescan     Leaves optical drive rescan mode.

Additional useful command-line options:
---------------------------------------
  config=<file>             Use an alternate Boot.plist file.

Options useful in the org.chameleon.Boot.plist file:
----------------------------------------------------
  Wait=Yes|No               Prompt for a key press before starting the kernel.
  "Quiet Boot"=Yes|No       Use quiet boot mode (no messages or prompt).
  Timeout=8                 Number of seconds to pause at the boot: prompt.
  "Instant Menu"=Yes        Force displaying the partition selection menu.

  "Default Partition"     Sets the default boot partition,
    =hd(x,y)|UUID|"Label"    Specified as a disk/partition pair, an UUID, or a
                             label enclosed in quotes.

  "Hide Partition"        Remove unwanted partition(s) from the boot menu.
    =partition               Specified, possibly multiple times, as hd(x,y), an
     [;partition2 ...]       UUID or label enclosed in quotes.

  "Rename Partition"      Rename partition(s) for the boot menu.
    =partition <alias>       Where partition is hd(x,y), UUID or label enclosed
     [;partition2 <alias2>   in quotes. The alias can optionally be quoted too.
     ...]

  GUI=No                  Disable the GUI (enabled by default).
  "Boot Banner"=Yes|No    Show boot banner in GUI mode (disabled by default).
  ShowInfo=No             Disables display of partition and resolution details.
                            "Boot Banner"=No will also disable this info.
  "Legacy Logo"=Yes|No    Use the legacy grey apple logo (enabled by default).

  RebootOnPanic=Yes|No    (disabled by default).

  EnableHiDPI=Yes|No      Enable High resolution display (disabled by default).

  BlackMode=Yes|No        The new BlackMode loads the white Apple logo,
                             instead of the gray Apple logo, on a black background (disabled by default).

  CsrActiveConfig=<value> Set CsrActiveConfig for OS 10.11.x, range 0..127 (default 0x67)

  PciRoot=<value>         Use an alternate value for PciRoot (default value 0).

  UseKernelCache=Yes|No   Yes will load pre-linked kernel and will ignore /E/E
						    and /S/L/E/Extensions.mkext.
							Default is No but Yes if you use Lion on a Raid partition.

  KeyLayout=keymap        Use to change the keyboard mapping of the bootloader
                            (e.g. KeyLayout=mac-fr)

  HDAEnabler=Yes|No         Automatic device-properties generation for audio controllers.
    HDEFLayoutID=<value>    Inject alternate value of layout-id for HDEF (HEX).
    HDAULayoutID=<value>    Inject alternate value of layout-id for HDAU (HEX).

  GraphicsEnabler=Yes|No    Automatic device-properties generation for graphics cards.
    SkipIntelGfx=Yes|No     Skip the Automatic device-properties generation for Intel cards.
    SkipNvidiaGfx=Yes|No    Skip the Automatic device-properties generation for Nvidia cards.
    SkipAtiGfx=Yes|No       Skip the Automatic device-properties generation for Ati cards.

    NvidiaGeneric =Yes|No   Use the classic Nvidia name for the SystemProfiler (disabled by default).
    NvidiaSingle =Yes|No    If you have two cards Nvidia and wants to inject only
                              first one then you can set this parameter (enbaled by default).

    AtiConfig=<cardcfg>   Use a different card config, e.g. AtiConfig=Megalodon.
    AtiPorts=<value>      Specify the number of ports, e.g. AtiPorts=2.
    UseAtiROM=Yes|No      Use an alternate Ati ROM image
                            (default path: /Extra/<vendorid>_<devid>_<subsysid>.rom)
    UseNvidiaROM=Yes|No   Use an alternate Nvidia ROM image
                            (default path:  /Extra/<vendorid>_<devid>.rom)
    VBIOS=Yes|No            Inject VBIOS to device-properties.
    display_0=<value>       Inject alternate value of display-cfg into NVDA,Display-A@0 (HEX).
    display_1=<value>       Inject alternate value of display-cfg into NVDA,Display-B@1 (HEX).
    IntelCapriFB=<value>    For Intel Ivy Bridge, range 0-11.
    IntelAzulFB=<value>     For Intel Haswell, range 0-16.
    IntelBdwFB=<value>      For Intel Broadwell, range 0-19.
    IntelSklFB=<value>      For Intel Skylake, range 0-12.
    InjectIntel-ig=<value>  Inject alternate value into AAPL,ig-platform-id (HEX).

    EnableBacklight=Yes	  Enable Back light option for NVIDIA and ATI
    EnableDualLink=Yes	  Enable DualLink option for NVIDIA and ATI
    EnableHDMIAudio=Yes	  Inject HDMI audio for NVIDIA and ATI.

  EthernetBuiltIn=Yes|No  Automatic "built-in"=YES device-properties generation
                          for ethernet interfaces.
  ForceHPET=Yes|No        Force HPET on (disabled by default).

  USBBusFix=Yes             Enable the XHCI, EHCI and UHCI fixes (disabled by default).
  EHCIacquire=Yes           Enable the EHCI fix (disabled by default).
  UHCIreset=Yes             Enable the UHCI fix (disabled by default).
  USBLegacyOff=Yes          Force USB Legacy off for XHCI and EHCI (disabled by default).
  XHCILegacyOff=Yes         Force USB Legacy off for XHCI (disabled by default).

  Wake=No                   Disable wake up after hibernation (enbaled by default).
  ForceWake=Yes             Force using the sleepimage (disabled by default).
  WakeImage=<file>          Use an alternate sleepimage file 
                              (default path is /private/var/vm/sleepimage).

  DropSSDT=Yes              Skip the SSDT tables while relocating the ACPI tables.
  Drop<file>=Yes            Skip:[HPET, SLIC, SBST, ECDT, ASF! or DMAR]

  DSDT=<file>               Use an alternate DSDT.aml file 
                              (default path: /DSDT.aml
                               /Extra/DSDT.aml bt(0,0)/Extra/DSDT.aml).
  HPET=<file>               Use an alternate HPET.aml file 
                              (default path: /HPET.aml
                               /Extra/HPET.aml bt(0,0)/Extra/HPET.aml).
  SBST=<file>               Use an alternate SBST.aml file 
                              (default path: /SBST.aml
                               /Extra/SBST.aml bt(0,0)/Extra/SBST.aml).
  ECDT=<file>               Use an alternate ECDT.aml file 
                              (default path: /ECDT.aml
                               /Extra/ECDT.aml bt(0,0)/Extra/ECDT.aml).
  ASFT=<file>               Use an alternate ASFT.aml file 
                              (default path: /ASFT.aml
                               /Extra/ASFT.aml bt(0,0)/Extra/ASFT.aml).
  DMAR=<file>               Use an alternate DMAR.aml file 
                              (default path: /DMAR.aml
                               /Extra/DMAR.aml bt(0,0)/Extra/DMAR.aml).
  APIC=<file>               Use an alternate APIC.aml file 
                              (default path: /APIC.aml
                               /Extra/APIC.aml bt(0,0)/Extra/APIC.aml).
  MCFG=<file>               Use an alternate MCFG.aml file 
                              (default path: /MCFG.aml
                               /Extra/MCFG.aml bt(0,0)/Extra/MCFG.aml).
  FADT=<file>               Use an alternate FADT.aml file 
                              (default path: /FADT.aml
                               /Extra/FADT.aml bt(0,0)/Extra/FADT.aml).

  GenerateCStates=Yes     Enable auto generation of processor idle sleep states
                            (C-States).
  GeneratePStates=Yes     Enable auto generation of processor power performance
                            states (P-States).
  CSTUsingSystemIO=Yes    New C-State _CST generation method using SystemIO
                            registers instead of FixedHW.

  EnableC2State=Yes       Enable specific Processor power state, C2.
  EnableC3State=Yes       Enable specific Processor power state, C3.
  EnableC4State=Yes       Enable specific Processor power state, C4.
  EnableC6State=Yes       Enable specific Processor power state, C6.
  EnableC7State=Yes       Enable specific Processor power state, C7.

  PrivateData=No          Show masked data (serial number) in bdmesg log (enbaled by default).

  ForceFullMemInfo=Yes    Force SMBIOS Table 17 to be 27 bytes long (disabled by default).

  SMBIOS=<file>             Use an alternate SMBIOS.plist file
                              (default path: /smbios.plist /Extra/smbios.plist
                               bt(0,0)/Extra/smbios.plist).

  SMBIOSdefaults=No         Don't use the Default values for SMBIOS overriding
                              if smbios.plist doesn't exist, factory
                              values are kept.


  KERNELPlist=<file>        Use an alternate kernel.plist file
                              (default path: /Extra/Kernel.plist
                               bt(0,0)/Extra/kernel.plist).

  KEXTPlist=<file>          Use an alternate kext.plist file
                              (default path: /Extra/Kext.plist
                               bt(0,0)/Extra/kext.plist).

  "Scan Single Drive"       Scan the drive only where the booter got loaded from.
    =Yes|No                 Fix rescan issues when using a DVD reader in AHCI mode. 
  Rescan=Yes                Enable CD-ROM rescan mode.
  "Rescan Prompt"=Yes       Prompts for enable CD-ROM rescan mode.
  SystemId=<UUID>         Set the system id manually to UUID.
                              Deprecated - Now SMsystemuuid in smbios.plist
                          SMUUID in smbios config (reserved field) isn't used then.
  SystemType=<n>          Set the system type to n, where n is between 0..6
                          (default=1 Desktop)
  md0=<file>              Load raw img file into memory for use as XNU's md0
                          ramdisk. /Extra/Postboot.img is used otherwise.
