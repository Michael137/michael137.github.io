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
  - When working off of an external drive make sure it is formatted as **Mac OS Extended (Case-sensitive, Journaled)**
- 16 GB RAM
  - The build might work with less but it will certainly test your patience
- Android Phone with unlocked bootloader
  - I use a Pixel 3 blueline in this writeup
  - Unlocking the bootloader should be trivial on most phones and doable with a different online source

# AOSP
The Android Open Source Project (AOSP) contains all that's necessary to boot an operational Android phone equipped with a basic set of apps. It comes with a default boot image which you can replace with your custom built of a device specific kernel. Below are the steps to build it.

1. Obtain and configure the AOSP source code
- Download Google's **repo** utility following [these instructions](https://source.android.com/setup/build/downloading#installing-repo)
- Choose the *Android branch* you want to build from [here](https://source.android.com/setup/start/build-numbers#source-code-tags-and-builds), e.g., android-4.0.1_r1
- Choose the *device build* you want to build from [this table](https://source.android.com/setup/build/running#selecting-device-build), e.g., aosp_blueline-userdebug
  - **Note: most likely you will want to user the "userdebug" extension as it enables developer features by default and is required by some of the next steps**
- Run:
~~~bash
repo init -u https://android.googlesource.com/platform/manifest -b android-4.0.1_r1
repo sync -qc -j12
build/envsetup.sh
lunch aosp_blueline-userdebug
~~~
2. Extract Vendor Binaries
- The steps outlined in the [Android Docs](https://source.android.com/setup/build/downloading#obtaining-proprietary-binaries) should suffice
- For Pixel devices obtain the binaries [here](https://developers.google.com/android/drivers). Make sure you download both the Google **and** the Qualcomm drivers and run the extraction script at the root of your AOSP directory
3. Compile
- Run: `make -j14`
- **(Troubleshooting)** Inevitably you will run into compilation errors. Check the following steps to see if one of my fixes applies:
		- **UTF-8 encoding**: `export JAVA_TOOL_OPTIONS=-Dfile.encoding=UTF8`
		- See [this SO post](https://stackoverflow.com/questions/26067350/unmappable-character-for-encoding-ascii-but-my-files-are-in-utf-8)
  		- **ld: symbol(s) not found for architecture i386**: your MacOS SDK is too recent. Select the SDK that is supported by your AOSP build
  		- **ERROR: Couldn't create a device interface iterator**: your fastboot is outdated for MacOS (probably because it's using the one you built with AOSP). [Download the latest fastboot and adb](https://android.stackexchange.com/questions/209725/fastboot-devices-command-doesnt-work-after-macos-high-sierra-10-14-4-upgrade) and use these
  		- **Fastboot operation not supported**: Flash the factory image for the operating system you are replacing and retry the fastboot flashall
  		- **Could not find a supported Mac SDK**: see [this SO post](https://stackoverflow.com/questions/50760701/could-not-find-a-supported-mac-sdk-10-10-10-11-10-12-10-13) for more details
						- You can download an old SDK by following [these instructions](https://roadfiresoftware.com/2017/09/how-to-install-multiple-versions-of-xcode-at-the-same-time/)
    					- Make sure **xcodebuild -showsdks** returns a SDK compatible with the AOSP build
    					- **Symbol not found: _OBJC_IVAR_$_NSScroller._action**: xselect Xcode
    					- Alternatively you can also copy the SDK into the existing Xcode.app: Download from [here](https://github.com/phracker/MacOSX-SDKs/releases) and copy it to **/Applications/Xcode.app/Contents/Developer/Platforms/MacOSX.platform/Developer/SDKs**
		- **sed: illegal option -- z**: [see this fix](https://stackoverflow.com/a/46859893/3842406)
        - **sepolicy_tests** failure: Quick fix which worked for my needs was to use `make SELINUX_IGNORE_NEVERALLOWS=true -j14` to compile. You can also apply the patch from [this promising Google Groups thread](https://groups.google.com/forum/?fromgroups#!topic/android-building/_VyLXSosgoo)
- **Result**: the resulting images should be located in **$AOSP_ROOT/out/target/product/blueline**

4. Flash the device
- If you have quit the terminal you built AOSP in re-run the `build/envsetup.sh` and `lunch ...` commands
- Reboot into the bootloader: `adb reboot-bootloader`
- Run: `fastboot flashall -w`
5. Install APKs (e.g., Play Store, Chrome, etc.)
- Follow [this SO post](https://stackoverflow.com/questions/41695566/install-google-apps-on-aosp-build/41818710#41818710)
		- In the above guide, PrebuiltGmsCore is renamed PrebuiltGmsCorePi in the Android 9 build
		- To be able to push files run:
~~~bash
adb disable-verity
adb root
adb remount
adb shell mount -o rw,system /;
~~~
		- Once installed, if the phone enters a bootloop this is likely due to whitelisting issues. You will have to add the neccessary permissions to the **/etc/permissions/privapp-permissions-blueline** file. To get the package name and permissions to add follow the [Android docs](https://source.android.com/devices/tech/config/perms-whitelist) on this topic. In short you have do the following:
~~~bash
adb pull /system/build.prop
vim build.prop # edit ro.control_privapp_permissions=log
adb push build.prop /system
adb reboot
adb shell
~~~

		- Search the `adb shell logcat` output for the string "Privileged permission". Now add a permissions whitelist file for your device into /etc/permissions. E.g., for Pixel 3 blueline it is **/etc/permissions/privapp-permissions-blueline**
		- `adb reboot`
	- **Troubleshooting**: For Android 10 you can download the Google Services Framework (GSF), Play Services (GMS) and [Phonesky](https://androidforums.com/threads/what-exactly-is-phonesky-apk.755972/) APKs and install them to the directories above. For Android 10 (i.e., not Android Pie), install the GMS APK to /system/priv-app/GmsCore/GmsCore.apk. Then add the necessary whitelist permissions as outlined above. See [this SO post](https://android.stackexchange.com/questions/209781/install-google-play-services-without-google-play-store) and [this forum post](https://www.element14.com/community/community/designcenter/single-board-computers/riotboard/blog/2014/05/14/installing-google-play-services-and-google-play-store-on-riotboard) for more info on these APKs

- (For **Chrome**): Download and [install](https://stackoverflow.com/questions/7076240/install-an-apk-file-from-command-prompt) the Chrome browser APK

# Customizing, Building, Embedding and Flashing Kernel
The kernel and AOSP projects are separate. To change the kernel of an AOSP build one has to check out and build the kernel image separately, embed it into the AOSP tree, rebuild the boot image and reflash the device.

Instructions are mostly from [Android docs](https://source.android.com/setup/build/building-kernels) to build and embed the kernel in the AOSP tree

1. Build kernel
2. Copy `Image.lz4` file to some `<DIR>`
3. `export TARGET_PREBUILT_KERNEL=<DIR>/Image.lz4`
4. From the AOSP root run: `m bootimage -j12`
8. `adb root`
9. `adb remount -R`
5. Copy the *.ko files from the kernel build output to `/vendor/lib/modules` on your device
  - `adb push <KERNEL SOURCE>/out/android-msm-pixel-4.9/dist/*.ko /vendor/lib/modules`
6. `adb reboot-bootloader`
6. `fastboot flash boot out/target/product/blueline/boot.img`
7. Reboot
8. Check kernel version in adb shell using `uname -a` or `cat /proc/version`

(Above instructions were partly taken from [this thread on a broken touchscreen driver](https://groups.google.com/forum/#!topic/android-building/ou630PviyDc))

## Customizing Kernel Configuration
For modern Kernels the defconfig can be specified in the top-level `build.config` file. The custom defconfig file should be called `<custom name>_defconfig`.
1. Navigate to `private/msm-google/arch/arm64/configs/`
2. `cp <defconfig template> <custom name>_defconfig`
  - For Pixel 3 Android 10 blueline the config is **b1c1_defconfig**
3. Make any changes to your custom config
4. Comment out the `POST_DEFCONFIG_CMDS` line in `build.config`
  - These perform `check_defconfig` and `update_nocfig_config` which will prevent you from customizing the defconfig
5. Comment out following chunk of the `prepare3` target in the main kernel makefile (e.g., `msm/private/msm-google/Makefile`):
~~~bash
ifneq ($(KBUILD_SRC),)
...
... $(srctree) is not clean, please run 'make mkrpoper'
endif
~~~
  - By default the build does not allow modifications to the source. By commenting out the above check the compilation should now go through with your custom defconfig
6. Follow [these steps](https://unix.stackexchange.com/q/421479) to save the custom defconfig configuration. These steps are outlined below (run from kernel source root, i.e., `msm/private/msm-google`)
  - `make ARCH=arm64 <custom name>_defconfig`
  - `diff -u .config ./arch/arm64/configs/<custom name>_defconfig | diffstat` should show your additions and potentially some more added by the build system
  - `grep <your added feature> .config` should show your features added
  - make ARCH=arm64 savedefconfig
  - `grep <your added feature> defconfig` should still show the additions you are interested in
7. Run `build/build.sh` from the root of the overall msm build repo (not necessarily the kernel source)

# The Silver Bullet (When things go wrong)

1. Boot into fastboot bootloader menu (press power and volume down for a few seconds)
2. Download and unzip factory image from [here](https://developers.google.com/android/images)
3. Run `./flash-all.sh`
	- Note: this will take some time if data wasn't wiped in advance
4. Now go back to the custom build
