---
layout: post
published: true
title: Linux adsprpc Driver Perf Infrastructure
date: '2020-05-23'
---
CPU statistics are readily available to the user. However, workloads on mobile phones run across dozens of other hardware components. To reason about the behaviour of IP blocks on mobile phones something along the lines of performance counters would go a long way. Below we the outline performance counter infrastructure built into the Linux MSM Kernel DSP driver. Since it is done on an adhoc basis between drivers, we outline the same for other hardware components in future blog posts.

The Linux [adsprpc driver](https://android.googlesource.com/kernel/msm/+/android-msm-flo-3.4-kitkat-mr1/Documentation/arm/msm/adsprpc-drv.txt) dictates communiction between applications and a DSP. Most of the driver source sits in [adsprpc.c](https://android.googlesource.com/kernel/msm/+/android-msm-marlin-3.18-nougat-dr1/drivers/char/adsprpc.c). The kernel implements the adsp subsystem as a character device driver. User-space applications can invoke the DSP using the IOCTL interface.

# Infrastructure
- Available counters: these are enumerated in `enum fastrpc_perfkeys`
  1. `PERF_COUNT`   : bookkeeping
  2. `PERF_FLUSH`   : cache flushes
  3. `PERF_MAP`     : fastrpc mmap calls and ion mappings
  4. `PERF_COPY`    : page copies
  5. `PERF_LINK`    : fastrpc_invoke_send calls (i.e., smd_write and glink_tx calls)
  6. `PERF_GETARGS` : get_args calls
  7. `PERF_PUTARGS` : put_args calls
  `PERF_INVARGS`
  `PERF_INVOKE`
  `PERF_KEY_MAX`