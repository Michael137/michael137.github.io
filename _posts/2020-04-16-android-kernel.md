---
layout: post
published: true
title: Building AOSP and the Android Kernel on MacOS (Catalina)
date: '2020-04-16'
---
**Disclaimer: This process reproducibly works on MacOS Catalina 10.15.4 at this point in time with the tool versions as listed in the "Requirements" section. This does not guarantee that the build will succeed on other versions. While the the overall steps should more or less remain the same the specific troubleshooting suggestions might not.**

Below steps are largely taken from the official [Android Docs](https://source.android.com/setup/build/building) but contain numerous clarifications and MacOS specific quirks.

# Requirements
- 250 GB free space
  - I built AOSP on an external SSD
- 16 GB RAM
  - The build might work with less but it will certainly test your patience
- Android Phone with unlocked bootloader
  - I use a Pixel 3 blueline in this writeup
  - Unlocking the bootloader should be trivial on most phones and doable with a different online source

# AOSP
The Android Open Source Project (AOSP) contains all that's necessary to boot an operational Android phone equipped with a basic set of apps. It comes with a default boot image which you can replace with your custom built of a device specific kernel. Below are the steps to build it.

1. Obtain the source


2. Extract Vendor Binaries
3. Build AOSP
4. Flash the device
5. Install APKs

# Kernel

# Embedding the kernel

# The Silver Bullet (When things go wrong)