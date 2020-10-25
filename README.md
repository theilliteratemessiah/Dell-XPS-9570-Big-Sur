# Table of contents
1. [Introduction](#dell-xps-9570---big-sur-and-catalina)
2. [Disclaimer](#disclaimer)
3. [What works](#what-works)
4. [What doesn't work](#what-will-not-work)
5. [Preparation](#preparation)
	a. [Catalina](#for-catalina)
	b. [Big Sur](#for-big-sur)
6. [EFI](#efi)
7. [UEFI Configuration](#uefi-configuration)
8. [Special consideration for 4K screen](#special-consideration-for-4k-screen)
9. [Installation](#installation)
10. [Post-installation](#post-installation)
	a. [Copy EFI](#copy-efi-files-required)
	b. [CPU](#cpu-optional)
11. [Known issues and fixes](#known-issues-and-fixes)
	a. [iMessage not working](#1-imessage-not-working)
	b. [Trackpad not working](#2-trackpad-not-working-after-fresh-boot)

# Dell XPS 9570 - Big Sur and Catalina
This Hackintosh configuration is proven on the **Dell XPS 9570 15" 4K** and is based on **OpenCore**.
It is suitable for use as a daily driver, even with Big Sur betas.
Tested up to Big Sur Beta 10.
For FHD display use the [config_fhd.plist](/blob/main/EFI/OC/config_fhd.plist) file and rename it to config.plist under /EFI/OC/.

# Disclaimer

It is assumed that the reader has a basic understanding of operating system installation, preparedness for failures/setbacks and a desire to succeed. Oh, and an hour or two of your time, but it pays out rather well.

* This configuration is designed for the 15" model of the Dell XPS 9570
* Tested on:
	* Big Sur, up to beta 10, and on Catalina
	* Hardware configuration:
		* CPU: 2.9 GHz 6-Core Intel Core i9
		* Memory: 32 GB 2667 MHz DDR4
		* SSD: Sabrent 1.0 TB
		* GPU: Intel UHD Graphics 630
* The stock wireless and bluetooth card needs to be replaced.

> 
> **Learn from others mistakes:** Do not open the Config.PLIST file with 'Clover Configurator' or like me you will lose an afternoon in diagnostics and Time Machine restore.
> 
> **This is one way street:** The Windows 10 will be removed and all data will be lost. Make appropriate backups - Windows 10 recovery drive (USB, 16GB) and backup your files.
> 
# What works
### Hardware 
1. **Intel iGPU** is supported with:
	* Both 2560x1440@144Hz over HDMI and a USB-C adapter
	* 4k@60Hz over DisplayPort
2. **Trackpad**: Full gesture support
3. **Speakers and Headphones**: The headphones connectivity can be intermittent at times, especially after resume from sleep. Please refer to the installation instructions section for guidance on the solution.
4. **Built-in Microphone**: Working as expected.
5. **Webcam**:  Working as expected.
6. **Wi-Fi/BT**: The stock WiFi/Bluetooth card (Killer) is not supported and has to be replaced. With a compatible card installed the system is stable and all features function as expected. Tested with the BCM94360NG card bought at eBay for about £40.
7. **Touch screen**: Working as expected and is beneficial in particular on the Big Sur betas. Disable it in UEFI if this feature is not required and battery life concerns arise.
8. **USB C**: USB-C charging of other devices works and display over Thunderbolt, with a suitable adapter, works well. Tested with an Anker USB-C hub adapter. Cost - £22.

### macOS Features
9. **Handoff**: Working as expected.
10. **iMessages and App Store**
Follow the instructions to configure your own Serial Number, SMUUID, etc.
11. **Unlock with Apple Watch**: Working and tested with Apple Watch 1 and Apple Watch 6.
12. **Backup wirelessly to a Time Capsule**: Working as expected.

## What will not work
1. **Nvidia GPU**: Apple dropped support for Nvidia GPUs with the release of Mojave. This has been disabled in the configuration and it is advised not to enable it.
2. **Killer Wifi/Bluetooth**
3. **Fingerprint reader**
4. **Samsung SSD**: Samsung SSD, in particular model PM981, does not work well and causes installation artefacts and kernel panics. It is best replaced. (P.S. NVMEFix does not work either.)
5. **SD Card Reader**: Perhaps owing to the Goodix drivers being unavailable for macOS. This repository will be amended if a solution is found.

# Preparation
This section has been written to more approachable and I appreciate that the power users will be able to optimise their workflows.

What is needed:
1. An Apple computer or a virtual machine with macOS
2. A USB drive with 16 GB (or more) storage
3. A copy of the installer
4. Python 3 

Connect your USB drive to the computer/virtual machine.
### For **Catalina**:
1. Follow the instructions [here](#what-will-not-work) to prepare a USB drive.

### For **Big Sur**:
1. [Enrol your device](https://beta.apple.com/sp/betaprogram/) and
	a. Install the agent
	b. Let it download the installer
This process can take a long time. After downloading macOS Big Sur, the installer will automatically launch. Close the installer.
2. Open Finder → Applications. Right-click on _Install macOS Big Sur_  → Show Package Contents.
3. Open Contents → Resources.
4. Launch a new Terminal window by going to Applications → Utilities → Terminal.
5. Type **sudo** followed by a space in the Terminal window.
6. Drag **createinstallmedia** to the Terminal window from the Resources folder.
7. Still within Terminal, type **--volume** followed by a space. 
(Please note that there are two hyphens before volume but the GitHub may show these as a single hyphen.)
8. Open Finder → Go → Go To Folder…
9. In the ‘_Go to the folder’_ box type **/Volumes** and click the Go button.
10. Drag the USB flash drive volume into the Terminal window.
The complete command must look like this:
```
sudo /Applications/Install \ macOS\ Big\ Sur\ Beta.app/Contents/Resources/createinstallmedia --Volume 'USBDrive'
```
11. Press the _Return_ key and, when prompted, enter your password.
12. When prompted type a “y,” and press the _Return_ key on the keyboard to submit.
(Terminal may ask for access to files on the removable volume. Click 'OK' to approve access.)

Have patience as this process can take a long time. (as much as 40 minutes, or perhaps even longer if using a virtual machine.)

## EFI 
Download this repository on your computer. Use a tool that permits mounting the EFI folder and copy the contents of this repository to the EFI folder of the USB drive.

## UEFI Configuration
1. Disable Secure Boot
2. Change your SATA Operation to AHCI mode
(Windows 10 will become inoperable after this step.)
3. Disable the SD Card reader
The following are optional:
4. Disable TPM 2.0.
5. Disable VT-d under visualization.
6. Disable Fast Boot.

> **If the USB drive is not shown during the installation phase**: Enable Legacy ROM by navigating to Boot Sequence and then changing from UEFI mode to Legacy mode.

## Special consideration for 4K screen
With Big Sur the 4K variant of Dell XPS 9570 may lead to blank screen during installation. If an external display is connected then it works.

1. Save this to a file and name it `edid.py` on your Desktop.
```
from subprocess import check_output
from base64 import b64decode, b64encode

def shout(cmd) -> str:
    '''sh cmd then return output
    '''
    return check_output(cmd, shell=True, encoding='utf-8').strip()

edid = shout('ioreg -lw0 | grep -i "IODisplayEDID"')
edid = edid.split('<')[1].split('>')[0]
edid = edid[:108] + 'a6a6' + edid[112:]
data = [int(edid[i:i+2], 16) for i in range(0, len(edid), 2)]
checksum = hex(256 - sum(data[:-1]) % 256)[2:]
data[-1] = int(checksum, 16)
data = b64encode(bytes(data)).decode('utf-8')
print(data)
```
2. Run the following commands in Terminal:
```
cd Desktop/
python3 edid.py
```
3. Open the Config.Plist file with a text editor
4. The go to `DeviceProperties > Add > PciRoot(0x0)/Pci(0x2,0x0)` add a new key named `AAPL00,override-no-connect` and put the data from step 2 in `<data></data>`.
# Installation

Press F12 to launch the installer from the external USB drive and perform installation as you would on a regular Apple computer.

# Post-installation
### Copy EFI files (Required)
To permit computer boot from the internal storage follow these steps:
1. Mount the EFI of the local SSD:
`sudo diskutil mount disk0s1`
2. Copy the EFI data from this repository.

### CPU (Optional)
#### 1. Kext generation
Use the script [one-key-cpufriend](https://github.com/stevezhengshiqi/one-key-cpufriend) from the `tools` folder to generate your own `CPUFriendDataProvider.kext`.
#### 2. Underclocking 
Undervolting your CPU can reduce heat, improve performance, and provide longer battery life. However, if done incorrectly, it may cause an unstable system. The  `tools`  folder contains a patched version of  [VoltageShift](https://github.com/sicreative/VoltageShift).

Using  `./voltageshift offset <CPU> <GPU> <CPUCache>`  you can adjust the voltage offset for the CPU, GPU, and cache. Safe starting values are  `-100, -75, -100`. From there you can start gradually lowering the values until your system gets unstable.

# Known issues and fixes
### 1. iMessage not working
You need to generate your own serial numbers. 

Use [Hackintool](https://www.tonymacx86.com/threads/release-hackintool-v3-x-x.254559/) and:
1. Go to the “Serial“ tab and make sure model is set to `MacBookPro15,2`
2. Use the barcode-with-apple button to check your generated serial numbers. If the website tells you that the serial number isn't valid, everything is fine. Otherwise, you have to generate a new set.
3. Next you will have to copy the following values from Hackintool to your  `config.plist`:
-   Serial Number ->  `Root/PlatformInfo/Generic/SystemSerialNumber`
-   Board Number ->  `Root/PlatformInfo/Generic/MLB`
-   SmUUID ->  `Root/PlatformInfo/Generic/SystemUUID`

Reboot and Apple services should work.

If they don't, follow  [this in-depth guide](https://dortania.github.io/OpenCore-Post-Install/universal/iservices.html). It goes deeper into ROM, clearing NVRAM, clearing Keychain (missing this step might cause major issues), and much more.

### 2. Trackpad not working after fresh boot
This very rarely crops up and can be remedied with a system restart.
1. Launch Terminal.
2. Type ``` sudo reboot ``` and press the return key.
3. Provide your password and let the computer restart.

> **Also worth noting** 
> The “PrintScreen“ button toggle the trackpad.

# References
Worth mentioning the hard work of:
1. [Jaro Meyer](https://github.com/jaromeyer/XPS9570-Catalina)
2. [Luletter Soul](https://github.com/LuletterSoul/Dell-XPS15-9570-macOS)
3. [Bavarian Cake](https://github.com/bavariancake/XPS9570-macOS)
4. [One-Key-CPUFriend](https://github.com/stevezhengshiqi/one-key-cpufriend)
5. [VoltageShift](https://github.com/sicreative/VoltageShift)
