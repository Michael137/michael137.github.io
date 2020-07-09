---
layout: post
published: true
title: DSP Activity Through Linux debugfs(8)
date: '2020-07-08'
---
*Disclaimer: This post refers to the Qualcomm Snapdragon 8xx chipsets but likely applies to other vendors as well.*

**Summary**: `cat /sys/kernel/debug/adsprpc/<your process name>`

# Why?
On today's mobile SoCs you typically see several flavours of DSPs including mDSP (modem), sDSP (sensor), cDSP (compute). The latter is increasingly used to accelerate user-space applications. Offloading to a DSP starts by calling the `adsprpc` driver. I was interested to see the offload in action and confirm which DSP I'm running on.

# And debugfs Saves the Day Again
If you have `debugfs(8)` enabled (this is set in your kernels defconfig) you will have access to the `/sys/kernel/debug/` directory on your device. Recent versions of the adsprpc driver conveniently register two debugfs operations (read and open). The [read operation](https://github.com/realme-kernel-opensource/realme2pro_P-kernel-source/blob/f99e10e256055c9ac261ce3ee5c91d74f1e882b2/drivers/char/adsprpc.c#L2487) will print statistics on all open DSP connections. Once your application opens `/dev/adsprpc-smd` you will see an entry in the `/sys/kernel/debug/adsprpc` directory. Enjoy.

<iframe width="420" height="315" src="https://www.youtube.com/watch?v=qQp5Im0_sWk" frameborder="0" allowfullscreen="allowfullscreen">&nbsp;</iframe>
