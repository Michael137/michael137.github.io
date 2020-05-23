---
layout: post
published: true
title: Linux adsprpc Driver Perf Infrastructure
date: '2020-05-23'
---
CPU statistics are readily available to the user. However, workloads on mobile phones run across dozens of other hardware components. To reason about the behaviour of IP blocks on mobile phones something along the lines of performance counters would go a long way. Below we outline performance counter infrastructure built into the Linux MSM Kernel DSP driver. Since it is done on an adhoc basis between drivers, we outline the same for other hardware components in future blog posts.

The Linux [adsprpc driver](https://android.googlesource.com/kernel/msm/+/android-msm-flo-3.4-kitkat-mr1/Documentation/arm/msm/adsprpc-drv.txt) dictates communiction between applications and a DSP. Most of the driver source sits in [adsprpc.c](https://android.googlesource.com/kernel/msm/+/android-msm-marlin-3.18-nougat-dr1/drivers/char/adsprpc.c). The kernel implements the adsp subsystem as a character device driver. User-space applications can invoke the DSP using the IOCTL interface.