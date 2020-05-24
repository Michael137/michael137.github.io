---
layout: post
published: true
title: 'FastCV: Hardware Acceleration of Computer Vision'
date: '2020-05-24'
---
[Qualcomm's FastCV SDK](https://developer.qualcomm.com/software/fast-cv-sdk) promises hardware acceleration of computer vision applications, providing a slew of APIs for common signal processing tasks (e.g., filters) without ties to particular hardware.

Personally I'm interested in the behaviour of hardware accelerated software on mobile systems; this makes FastCV seem like an excellent research platform. However, open-source applications that make use of the framework are far in between. Qualcomm ships two demo Android applications: *fastcvdemo* amd *fastcvcorner*. Below are the not-so-straightforward steps to build these applications on a Pixel 3 (despite the decade old official documentation).

# Requirements
This is one of the rare instance where the IDE simplified the build process. Thus we'll be using Eclipse for Java, ADT and CDT on **Linux (Ubuntu 18.04)** to build the demo applicaitons. Other OSes are likely to be compatible with the below steps (Windows is the Qualcomm recommended OS).

- JDK 8 (make sure it is not higher than this or else you wont be able to produce a `*.apk` file)
- [Eclipse for Java](http://www.eclipse.org/downloads/)
- Android SDK Version 2.2 API Level 8 (install this through Android's SDK Manager)
- Android DDMS and ADT (these can be installed from inside Eclipse as plugins)
  - Refer to the **Android ADT** section of the [Qualcomm installation docs](https://developer.qualcomm.com/software/fast-cv-sdk/setting-up)
- Android NDK r6 (higher version may work but was not tested)
  - [Download NDK r6 Linux](https://dl.google.com/android/ndk/android-ndk-r6-linux-x86.tar.bz2)
  - [Other old versions](https://stackoverflow.com/a/28088215)
  - Place the Android NDK directory at the same level as the Android SDK directory
- C/C++ Development Toolkit (CDT) (install it through Eclipse again; the latest version works)

# Setup
setup path
setup NDK root
set eclipse's Java to correct version
set up projects android version
copy SDK (symlinks + set up correct build-tools and tools)
default.properties

We follow the 