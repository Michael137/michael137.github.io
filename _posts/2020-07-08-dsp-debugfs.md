---
layout: post
published: true
title: DSP Activity Through Linux debugfs(8)
date: '2020-07-08'
---
*Disclaimer: This post refers to the Qualcomm Snapdragon 8xx chipsets but likely applies to other vendors as well.*

**Summary**: `cat /sys/kernel/debug/adsprpc/<your process name>`

# Why?
On today's mobile SoCs you typically see several flavours of DSPs including mDSP (modem), sDSP (sensor), cDSP (compute). The latter is increasingly used to accelerate user-space applications. Offloading to a DSP starts by calling the `adsprpc` driver.