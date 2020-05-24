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
- Download the FastCV SDK and install it into the same directory where the Android SDK and NDK are located

The final directory structure should look like (modulo directory names):
```
$HOME/Android/android-ndk-r6/
$HOME/Android/android-sdk/
$HOME/Android/android-fastcv/
```

# Setup
## Environment Variables
Do the following in the same session where you start Eclipse. Or add the lines to your bashrc.

1. Add the SDK `platform-tools` and `tools` directories to your path:
```bash
export ANDROID_HOME=$HOME/Android/Sdk
export PATH=$ANDROID_HOME/tools:$PATH
export PATH=$ANDROID_HOME/platform-tools:$PATH
export PATH=$HOME/Android/android-ndk-r6:$PATH
```
2. Set the `ANDROID_NDK_ROOT` variable:
```bash
export ANDROID_NDK_ROOT=$HOME/Android/android-ndk-r6
```
## Eclipse
To prevent obscure errors later down the line make sure Eclipse uses JDK 8.

Follow [these instructions](https://stackoverflow.com/a/50164402) (essentially just editing **eclipse.ini** and repointing the installed JREs setting)

You can find the location of your Java installtion using:
`update-alternatives --list java`

## ADT
This step prevents the ADT error on Eclipse startup: **Failed to get the required ADT version number from the SDK**

This is because Eclipse ADT and Android are both using the same SDK tools. Correct this by creating a new SDK directory for Eclipse eclusively alongside the original SDK. Follow this [SO answer](https://stackoverflow.com/a/48153498/3842406) to create a new Eclipse Android SDK directory that contains symlinks to all subdirectories of the original Android SDK apart from the `tools/` folder (which you have to download separately).

You directory should look as follows:

```
$HOME/Android/android-ndk-r6/
$HOME/Android/android-sdk/
    ...
    tools/
$HOME/Android/android-sdk-eclipse/
    <symlinks to android-sdk for all subdirectories except tools/>
$HOME/Android/android-fastcv/
```

## FastCV SDK
download fastcv + copy files
## Import Projects
set up projects android version
default.properties

# Compile and Install Applications
build project
  (turn off lint if necessary)
export signed apk
adb install