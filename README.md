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

> **Learn from others mistakes:** Do not open the Config.PLIST file with 'Clover Configurator' or like me you will lose an afternoon in diagnostics and Time Machine restore.

## What works
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

