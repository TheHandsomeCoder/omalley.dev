---
title: Repurposing a Nexus 9 to watch streams on Discord
date: "2020-05-20"
description: Can we reuse old technology for a single purpose
---

COVID19 has upheaved everyones day to day lives mine being no exception. I'm working from home full time, I'm social distancing and I've had to get creative with most of by hobbies to keep enjoying them.

My D&D game has moved online with our DM opting to stream a copy of Fantasy Grounds via Discord and have each of us speak our actions though a group voice chat. This has worked pretty well for the most part, save for a few of our members only having access to phones currently which can make viewing the stream difficult. With my Nexus 9 gathering dust for a few years now I figured it was an opertune time to try repurpose it for one thing, watching streams on Discord.

The Nexus 9 was a premium tablet in 2014, a 9 inch display powered by a 64bit NVIDIA tegra K1 dual-core processor at 2.3GHz and 2GB of DDR3 memory. Subsiquent android updates and the addtion of full disk encryption caused the tablet to lag and become pretty unusable.

The objective for this project is install a stripped down version of Android (SlimROM), remove full disk encryption and install the Discord app on the tablet.

## Installing a custom recovery
By default you can't install a custom version android easily, we need to remove a few barriers first. The first being a custom recovery tool which will allow us to flash the new image.

A breif search of reddit and XDA Developers indicated that the custom recovery I'd used in the past (clockwork recovery) was no longer being actively developed and I should use a project called [TWRP](https://twrp.me/about/) instead. Their website was quite informative and most importantly had [support for the Nexus 9](https://twrp.me/htc/htcnexus9.html#fastboot-install). The link above details the steps required to install, but I will sumerise below.

* Downloaded the [latest version of TWRP for the Nexus 9](https://eu.dl.twrp.me/flounder/)
* Downloaded a [standalone version of Androids platform tools.](https://developer.android.com/studio/releases/platform-tools)
* Place the TWRP image in the same folder as your platform tools.
* Enabled USB debugging
    >On your device, go into Settings -> About and find the Build Number and tap on it 7 times to enable developer settings. Press back and go into Developer Options and enable USB debugging.
* Open a terminal and use adb to reboot to the bootloader `adb reboot bootloader`
* Use fastboot to flash the image `fastboot flash recovery twrp.img`
* Then use the on screen commands to reboot to recovery, this should allow you to boot TWRP.

## Installing the custom rom
Next we're going to install the custom rom. For this we need to downloaded [Slimrom for the Nexus 9](https://slimroms.org/#/device/flounder) and a copy of the [open source google apps](https://opengapps.org/). I've elected to go with pico as it's the smallest available to get the play store working.

* In TWRP, go to `Wipe -> Format Data`, this will remove the encryption on the data partition. 
* Go to `Advanced Wipe` and wipe all partitions
* In a terminal on your computer use adb to transfer over the slimrom and gapps images. 
    * `./adb push slimrom.zip /sdcard`
    * `./adb push gapps.zip /sdcard`
* In TWRP flash slimrom and then gapps
* Restart the tablet and it should now boot into slimrom.







