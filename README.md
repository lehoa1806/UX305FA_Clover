# UX305FA - Big Sur
Big Sur 11.1 on ASUS UX305FA

##  Hardware
* ASUS ZenBook UX305FA
* Intel(R) Core(TM) M-5Y10c
* Intel HD 5300 (1920 x 1080)
* 8 GB Ram
* Wifi and bluetooth: Replaced with Broadcom BCM94352Z
* BIOS version 213

##  Overview
Updated to Big Sur 11.1

Removed these following configurations since I don't use them at all.
* Ambient light sensing
* Keyboard: Function keys - Brightness 

###  What works: (necessity order)
* Keyboard
* Touchpad
* Video: Intel HD 5300
* USB
* Stock USB to Ethernet adapter
* Wifi and bluetooth (BCM94352Z)
* Audio: microphone, internal audio and HDMI audio
* Internal webcam
* Battery: Status and native power management
* Card reader
* Sleep

###  What doesn't work
* Intel wireless - definitely needs to be replaced
* No other things so far

###  Not tested
* File Vault 2
* Hibernation
* Or any other which I don't need

##  BIOS
* Restore Defaults
* Advanced/VT-d: Disabled
* Advanced/Graphics Configuration/DVMT Pre-Allocated: 128M
* Boot/Fast Boot: Disabled
* Boot/Lauch CMS: Disabled
* Security/Secure Boot Control: Disabled

##  OpenCore
* Latest version - 0.6.4
* [Install Guide](https://dortania.github.io/OpenCore-Install-Guide/)
* [Post-Instll Gude](https://dortania.github.io/OpenCore-Post-Install/)

## ACPI notes:
SSDTs for UX305FA:
- [SSDT-PLUG](https://dortania.github.io/Getting-Started-With-ACPI/Universal/plug)
- [SSDT-EC](https://dortania.github.io/Getting-Started-With-ACPI/Universal/ec-fix)
- [IRQ SSDT](https://dortania.github.io/Getting-Started-With-ACPI/Universal/irq)
- [SSDT-PNLF](https://dortania.github.io/Getting-Started-With-ACPI/Laptops/backlight)
- [SSDT-GPI0](https://dortania.github.io/Getting-Started-With-ACPI/Laptops/trackpad)
<br/>The first three can be generated by SSDTTime

## Install
* Create USB using createinstallmedia method
* Install OpenCore to USB (check the following image)
* Copy HFSPlus.efi from
[/drivers](https://github.com/lehoa1806/Asus-Maximus-IX-CODE-CLOVER/tree/master/drivers)
<p align="left">
  <img src="https://raw.githubusercontent.com/lehoa1806/UX305FA-OpenCore/master/images/ux305fa-bigsur.efi.png">
</p>

## Prepare AML files and Kexts
```
mkdir -p ~/workspace/HACKINTOSH/UX305FA/build/kexts
cd ~/workspace/HACKINTOSH/UX305FA/build/
mkdir acpi

cd ~/workspace/HACKINTOSH/UX305FA/build/
git clone https://github.com/corpnewt/ProperTree

cd ~/workspace/HACKINTOSH/UX305FA/build/
git clone https://github.com/corpnewt/GenSMBIOS
cd GenSMBIOS
chmod +x GenSMBIOS.command
./GenSMBIOS.command
# Choose one and copy to config.plist

# DSDT.aml needs to be dumped from Windows or Linux before running the following steps
cd ~/workspace/HACKINTOSH/UX305FA/build/
git clone https://github.com/corpnewt/SSDTTime.git
cd SSDTTime
chmod +x SSDTTime.command
./SSDTTime.command
# 1. Select D to load the DSDT.aml (drag and drop DSDT.aml to the terminal)
# 2. FixHPET: Select 1 -> C -> enter
# 3. FakeEC Laptop: Select 3 -> enter
# 4. PluginType: Selct 4 -> enter
# 5. Files are generated in Results under SSDTTime: Copy *.aml files to EFI/OC/ACPI/. Open patches_OC.plist and copy its content to EFI/OC/config.plist
cp -rp Results/SSDT-HPET.aml Results/SSDT-EC.aml Results/SSDT-PLUG.aml ~/workspace/HACKINTOSH/UX305FA/build/acpi/

# USBPorts.kext is generated by Hackintool

# Build
sudo xcode-select -s /Applications/Xcode.app/Contents/Developer

# Lilu
cd ~/workspace/HACKINTOSH/UX305FA/build
git clone https://github.com/acidanthera/Lilu.git
git clone https://github.com/acidanthera/MacKernelSDK
cd Lilu
cp -R ./../MacKernelSDK .
xcodebuild -configuration Debug
cp -R build/DEBUG/Lilu.kext ./../
xcodebuild
cp -R build/Release/Lilu.kext ./../kexts/

# WhateverGreen
cd ~/workspace/HACKINTOSH/UX305FA/build
git clone https://github.com/acidanthera/WhateverGreen.git && cd WhateverGreen
cp -R ./../MacKernelSDK ./../kexts/Lilu.kext
xcodebuild
cp -R build/Release/WhateverGreen.kext ./../kexts/

# VirtualSMC
cd ~/workspace/HACKINTOSH/UX305FA/build
git clone https://github.com/acidanthera/VirtualSMC.git && cd VirtualSMC
cp -R ./../MacKernelSDK ./../kexts/Lilu.kext .
xcodebuild
cp -R build/Release/VirtualSMC.kext build/Release/SMCBatteryManager.kext build/Release/SMCLightSensor.kext ./../kexts/

# AppleALC
cd ~/workspace/HACKINTOSH/UX305FA/build
git clone https://github.com/acidanthera/AppleALC.git && cd AppleALC
cp -R ./../MacKernelSDK ./../kexts/Lilu.kext .
xcodebuild
cp -R build/Release/AppleALC.kext ./../kexts/

# BrcmPatchRAM3
cd ~/workspace/HACKINTOSH/UX305FA/build
git clone https://github.com/acidanthera/BrcmPatchRAM.git && cd BrcmPatchRAM
cp -R ./../MacKernelSDK ./../kexts/Lilu.kext .
xcodebuild
cp -R build/Products/Release/BrcmBluetoothInjector.kext build/Products/Release/BrcmFirmwareData.kext build/Products/Release/BrcmPatchRAM3.kext ./../kexts/

# AirportBrcmFixup
cd ~/workspace/HACKINTOSH/UX305FA/build
git clone https://github.com/acidanthera/AirportBrcmFixup.git && cd AirportBrcmFixup
cp -R ./../MacKernelSDK ./../kexts/Lilu.kext .
xcodebuild
cp -R build/Release/AirportBrcmFixup.kext ./../kexts/

# CPUFriend
cd ~/workspace/HACKINTOSH/UX305FA/build
git clone https://github.com/acidanthera/CPUFriend.git && cd CPUFriend
cp -R ./../MacKernelSDK ./../kexts/Lilu.kext .
xcodebuild
cp -R build/Release/CPUFriend.kext ./../kexts/

cd ~/workspace/HACKINTOSH/UX305FA/build/
git clone https://github.com/corpnewt/CPUFriendFriend.git
cd CPUFriendFriend
chmod +x CPUFriendFriend.command
./CPUFriendFriend.command
# CPUFriendDataProvider.kext is generated in Results under CPUFriendFriend. Copy it to EFI/OC/Kexts/
cp -rp Results/CPUFriendDataProvider.kext ./../kexts/
```
## Big Sur
<p align="center">
  <img src="https://raw.githubusercontent.com/lehoa1806/UX305FA-OpenCore/master/images/ux305fa-bigsur.info.png">
</p>
<p align="center">
  <img src="https://raw.githubusercontent.com/lehoa1806/UX305FA-OpenCore/master/images/ux305fa-bigsur.Geekbench_5.png">
</p>

## Catalina
<p align="center">
  <img src="https://raw.githubusercontent.com/lehoa1806/UX305FA-OpenCore/master/images/Catalina_info.png">
</p>
<p align="center">
  <img src="https://raw.githubusercontent.com/lehoa1806/UX305FA-OpenCore/master/images/Catalina_geekbench5.png">
</p>

## Mojave
<p align="center">
  <img src="https://raw.githubusercontent.com/lehoa1806/UX305FA-OpenCore/master/images/overview-14.6.png">
</p>
<p align="center">
  <img src="https://raw.githubusercontent.com/lehoa1806/UX305FA-OpenCore/master/images/geekbench.5.0.2-14.6.png">
</p>

