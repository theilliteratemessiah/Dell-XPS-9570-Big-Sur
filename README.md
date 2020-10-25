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
8. [Installation](#installation)
9. [Post-installation](#post-installation)
# Dell XPS 9570 - Big Sur and Catalina

This Hackintosh configuration is proven on the **Dell XPS 9570 15"** and is based on **OpenCore**.
It is suitable for use as a daily driver, even with Big Sur betas.

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

# EFI 
Download this repository on your computer. Use a tool that permits mounting the EFI folder and copy the contents of this repository to the EFI folder of the USB drive.

### General instructions (post-install)
1. Launch a new Terminal window by going to Applications → Utilities → Terminal and type:
```
diskutil list
```
The output should look something like this on a SSD with 256 GB storage:
```
/dev/disk0
 #: TYPE                     NAME          SIZE       IDENTIFIER
 0: GUID_partition_scheme                  *251.0 GB  disk0
 1: EFI                                    209.7 MB   disk0s1
 2: Apple_HFS                Macintosh HD  250.1 GB   disk0s2
 3: Apple_Boot               Recovery HD   650.0 MB   disk0s3
```
Note the volume identifier of the EFI - disk0s1 in this example.

2. Create a mount point.
```
mkdir /Volumes/efi
```
3. Mount the EFI partition at the efi mount point by typing in
```
sudo mount -t msdos /dev/disk0s1 /Volumes/efi
```
4. Copy the contents of this repository to the EFI volume.

# UEFI Configuration
1. Disable Secure Boot
2. Change your SATA Operation to AHCI mode
(Windows 10 will become inoperable after this step.)
3. Disable the SD Card reader
The following are optional:
4. Disable TPM 2.0.
5. Disable VT-d under visualization.
6. Disable Fast Boot.

> **If the USB drive is not shown during the installation phase**: Enable Legacy ROM by navigating to Boot Sequence and then changing from UEFI mode to Legacy mode.

# Installation

Press F12 to launch the installer from the external USB drive and follow the instructions.

# Post-installation
