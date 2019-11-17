---
layout: single
title: BOM and configuration of ESXi hosts 
tags: homelab vsphere esxi
---

In this post I will discuss the BOM and installation process of a new vSphere cluster for my homelab.

### **Goals for the cluster**

* Host Support for 64Gb of RAM
* Host Support 8-core processor
* _Possibly_ VSAN capabale (2 M.2 slots)
* Built-in Intel LAN/Video
* Small form factor
* Quiet and minimal power consumption

### BOM
* 3x GIGABYTE Z390 M Gaming (Intel LGA1151/Z390/Micro ATX/M.2/Realtek ALC892/Intel GbE LAN/HDMI/Motherboards)
* 3x G.SKILL Aegis 64GB (4 x 16GB) 288-Pin DDR4 SDRAM DDR4 2400 (PC4 19200) Intel Z170 Platform / Intel X99 Platform Desktop Memory Model F4-2400C15Q-64GIS
* 3x Intel Core i7-9700K Desktop Processor 8 Cores up to 4.9 GHz Turbo Unlocked LGA1151 300 Series 95W
* 3x Titan DC-155A915Z/RPW 95mm Z-AXIS CPU Cooler
* 3x Rosewill - Black & Silver, 0.8mm SGCC Steel Slim Micro ATX Computer Case with ATX12V Flex 300W Power Supply - R379-M
* 3x ADATA 16GB UV140 Bookmarked, Capless USB 3.0 Flash Drive (AUV140-16G-RBE)
* 3x StarTech 6in 4 Pin to 8 Pin EPS Power Adapter with LP4 - F/M (EPS48ADAP)

##### Total Cost: $~2921 (12/20/18)

### ESXi Installation

The motherboards (as far as I can tell) only support UEFI for PXE boot. Since I install ESXi with a TFTP/HTTP server, I needed to find a way to install ESXi with UEFI rather than the legacy BIOS.

* Thank you as always to William Lam and his awesome blog I just followed the steps from his [post](https://www.virtuallyghetto.com/2015/10/support-for-uefi-pxe-boot-introduced-in-esxi-6-0.html) to enable this functionality.

This allowed the hosts to PXE boot and start the installation, but the build of ESXi 6.7 that I was using didn't recognize the onboard Intel NIC and the installation would halt and not proceed.

This is where [ESXi-Customer-PS](https://www.v-front.de/p/esxi-customizer-ps.html) comes to the rescue. I have used this tool in other homelab builds and packaging in old Realtek NIC drivers into an ESXi build. So I figured I would attempt to use the tool again to address the issue above.

The tool is no longer an EXE binary but rather built with a Powershell script. Tons of improvements and ease of use have been built into the script. 

This tool uses the VMware online depot to build any version of the _latest_ ESXi build from 5.0 to 6.7.

* Command used to build the ISO for the latest version of ESXi 6.7
    ```
    .\ESXi-Customizer-PS-v2.6.0.ps1 -v67
    ```

Extracted that ISO following Lam's post above and **BAM** ESXi installed successfuly and recognized the onboard NIC. Added the hosts to vCenter and I have a lot more horsepower added to my homelab.

Huge shoutout to William Lam and Andreas Peetz for saving me a ton of time and effort trying to make desktop hardware work with ESXi.