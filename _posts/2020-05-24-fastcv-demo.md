---
layout: post
published: true
title: 'FastCV: Hardware Acceleration of Computer Vision'
date: '2020-05-24'
---
[Qualcomm's FastCV SDK](https://developer.qualcomm.com/software/fast-cv-sdk) promises hardware acceleration of computer vision applications, providing a slew of APIs for common signal processing tasks (e.g., filters) without ties to particular hardware.

Personally I'm interested in the behaviour of hardware accelerated software on mobile systems; this makes FastCV seem like an excellent research platform. However, open-source applications that make use of the framework are far in between. Qualcomm ships two demo Android applications: *fastcvdemo* amd *fastcvcorner*. Below are the not-so-straightforward steps to build these applications on a Pixel 3 Android 10 (despite the decade old official documentation).

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

This is because Eclipse ADT and Android are both using the same SDK tools. Correct this by creating a new SDK directory for Eclipse eclusively alongside the original SDK. Follow this [SO answer](https://stackoverflow.com/a/48153498/3842406) to create a new Eclipse Android SDK directory that contains symlinks to all subdirectories of the original Android SDK apart from the `tools/` folder (which you have to download separately for version 25.x.x).

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

To avoid more errors later follow this [SO answer](https://stackoverflow.com/a/44916103/3842406): download `build-tools/` version 25.x.x Android Studio's SDK Manager and remove all other version from the newly created Eclipse Android SDK `build-tools/` directory.

**IMPORTANT**: Point Eclipse to your new Android SDK in **Window > Preferences > Android > SDK Location**. Make sure you see Android 2.2 API Level 8 in the list of available SDKs.

## FastCV SDK
Copy the FastCV headers and static library to the Android NDK.

**Note: make sure you don't copy the library or header anywhere else in the NDK apart from `android-8/` to prevent confusing build system errors

```
mkdir $HOME/Android/android-ndk-r6/platforms/android-8/arch-arm/usr/include/fastcv
cp $HOME/Android/android-fastcv/inc/fastcv.h $HOME/Android/android-ndk-r6/platforms/android-8/arch-arm/usr/include/fastcv/
cp $HOME/Android/android-fastcv/lib/Android/lib32/libfastcv.a $HOME/Android/android-ndk-r6/platforms/android-8/arch-arm/usr/lib
```

## Import and Compile
[Qualcomm's installation instructions](https://developer.qualcomm.com/software/fast-cv-sdk/sample-app) are mostly accurate.

1. Import the Android project from existing code (pointing Eclipse to the FastCV SDK directory when prompted).
2. Change the Android SDK path in **Project > Properties > Android** to API Level 8 and target *Android 2.2* (it will work even on Android 10)
3. Disable **...abort if fatal errors are found** in **Window > Preferences > Lint Error Checking**
4. **IMPORTANT**: Add a `default.properties` file in the project root and add the single line:
```target=android-8``` (see [this obscure thread](https://developer.qualcomm.com/forum/qdn-forums/add-advanced-features/computer-vision-fastcv/27256))
5. Convert to CDT C++ Project as specified in the [Qualcomm docs](https://developer.qualcomm.com/software/fast-cv-sdk/sample-app)
6. **Project > Clean... > Clean**
7. **Project > Build All**

## Install applications on device
Contrary to what the Qualcomm docs say, the `bin/` directory doesn't necessarily contain an `*.apk` file which you need to transfer the application to your Android device.

1. Right click your project
2. Select **Android Tools > Export Signed Application Package**
3. Create a keystore with any password in any location (you can reuse this whenever you export another application)
4. If you chose `bin/` as your output directory in step 3 then you can install the `bin/<demo>.apk` to your connected device by:
`adb install bin/<demo>.apk`
5. The applications should be visible and runnable on your phone

# loadjpeg
There is a third demo application in the FastCV SDK called **loadjpeg**. However, it [does not work out-of-the-box](https://developer.qualcomm.com/forum/qdn-forums/software/fastcv-computer-vision-sdk/29814). Following steps fix the application:

1. Add an *intent-filter* to the `AndroidManifest.xml`:
```
<intent-filter>
	... original intent filters ...
</intent-filter>
<intent-filter>
	<action android:name="android.intent.action.MAIN" />
    <category android:name="android.intent.category.LAUNCHER" />
</intent-filter>
```

2. Change all occurences of *fastcvsample* in `jni/Anroid.mk` to *loadjpeg* (as suggested [here](https://developer.qualcomm.com/forum/qdevnet-forums/computer-vision-fastcv/18122))
3. Build and install the project
4. You might run into permission issues after starting the application (run `adb shell logcat` to see this). In this case make [these additions](https://stackoverflow.com/a/41221852/3842406) to the `LoadJpeg.java`