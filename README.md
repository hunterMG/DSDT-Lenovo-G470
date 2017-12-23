
# Hackintosh 10.11.x
([10.12.x version here](https://github.com/hunterMG/DSDT-Lenovo-G470/tree/Sierra))
## MaciASL 
https://bitbucket.org/RehabMan/os-x-maciasl-patchmatic/downloads

## Audio
    VoodooHDA.pkg

## USB
    1.USBInjectAll.kext 
    2.Add USBBusFix=No in org.chameleon.BOOT.plist
    3.Visit chrome://flags/#enable-webusb, and disable it to avoid likely problems.

## Battery status
    1. patch the DSDT with Battery.txt
    2. install ACPIBatteryManager.kext

## Backlight
    1. Apply patch "Brightness Fix" on DSDT
    2. install IntelBacklight.kext

    backlight key (refer: https://github.com/RehabMan/OS-X-ACPI-Keyboard)
    brightness down: EC _Q11
                 up: EC _Q12
    actually they're not needed to change, brightness up :Fn + Home ->F15,
                                                     down:Fn + PgUp ->F14
## VGA
    0. SMBios -> MacBookPro8,1
    1. patch DSDT with VGA.txt
    2. fix AppleIntelSNBGraphicsFB.kext with hex editor.
Open AppleIntelSNBgraphicsFB in AppleIntelSNBgraphicsFB.kext/Content/MacOS/ with hex editor, then find  `0102 0400 1007 0000 1007 0000`,it indicates 4 display port of machine(1 for the internal LCD monitor ,the next 4 lines describe the 4 ports), the 1st line is about the internal monitor(`0503 0000 0200 0000 3000 0000`) the 2nd line is about HDMI port(`0205 0000 0004 0000 0700 0000`) if you have one. Because G470 has no HDMI port, so replace the 2nd line with `0602 0000 0001 0000 0900 0000`, it describes a VGA port. Actually, because Mac has no VGA port, it's obvious that its driver has no infomation about VGA port, this line is copy from other place.

## Fix Intel HD3000 memory size (10.11＋ need to disable SIP first; use the backup kext if failed)
```
384m->512m:
cd /S*/L*/Ext*/AppleIntelSNBGraphicsFB.kext/C*/M*
sudo cp AppleIntelSNBGraphicsFB AppleIntelSNBGraphicsFB.backup
sudo perl -pi -e 's|\xC7\x45\xBC\x00\x00\x00\x18|\xc7\x45\xBC\x00\x00\x00\x20|g' AppleIntelSNBGraphicsFB
sudo touch /S*/L*/Extensions
```
```
384m->1G
cd /S*/L*/Ext*/AppleIntelSNBGraphicsFB.kext/C*/M*
sudo cp AppleIntelSNBGraphicsFB AppleIntelSNBGraphicsFB.backup
sudo perl -pi -e 's|\xC7\x45\xBC\x00\x00\x00\x18|\xc7\x45\xBC\x00\x00\x00\x40|g' AppleIntelSNBGraphicsFB
sudo touch /S*/L*/Extensions
```
```
512m->1G
cd /S*/L*/Ext*/AppleIntelSNBGraphicsFB.kext/C*/M*
sudo cp AppleIntelSNBGraphicsFB AppleIntelSNBGraphicsFB.backup
sudo perl -pi -e 's|\xC7\x45\xBC\x00\x00\x00\x20|\xc7\x45\xBC\x00\x00\x00\x40|g' AppleIntelSNBGraphicsFB
sudo touch /S*/L*/Extensions
```