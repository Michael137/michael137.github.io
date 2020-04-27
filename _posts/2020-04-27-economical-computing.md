---
layout: post
published: true
title: Economical Computing
subtitle: Sharing Economy of Compute
date: '2020-04-27'
---
The ASPLOS *Wild and Crazy Ideas* (WACI) annually collates ambitious, yet possibly "reasonable", projects that span hardware, systems and programming languages research. This is my colleague Yeongil's and my entry for 2020's WACI that we were supposed to present in Zurich:

```
Exchange of goods has been the driving force of our modern economy. Increasingly does society turn to the model of a sharing economy. Surpluses of resources are shared to prevent waste, for instance, cars (Uber), accommodation (AirBnB) or food (Olio). Why is it then that the same is not done with compute resources?

As division of labor initiated the industrial revolution, specialized hardware is initiating a new era of computing. Most systems-on-chip (SoC), including your phone, smartwatch, etc., are equipped with powerful accelerators to execute user's needs fast and with low power. However, this capability is kept within the boundary of the device itself without a way to share it with others in need. Imagine making a laggy video call on your laptop while having your brand-new smartphone sitting idle in your pocket. Then imagine taking your phone and placing it on your desk, and voila, the video is now running through the fancy image signal processor, making the video snappy and crisp. This is our vision of "economical computing": we aim to break the boundaries of modern computer systems. 

Our proposal involves the redesign of hardware, operating systems and programming language abstractions to facilitate the sharing of compute resources.

Any device should be able to utilize the idle hardware modules as needed from any nearby device, including accelerators, memory subsystems, interconnects, batteries, etc. This requires us to rethink the current hardware abstractions and work towards unified interfaces that allow composability, extensibility and communication between arbitrary hardware. Additionally, an SoC's interface needs to expose the functionality of each of its components such that the software above it is able to offload its tasks appropriately.

The operating system needs support inter-device communication with neighboring operating systems, include an neighbor-resource aware dynamic scheduler to borrow or lend resources under consent, prevent resource lending when running critical applications and protect the device from malicious resource theft.

We need to rethink our traditional programming models. Users need to be able to provide device affinity, indicate suitable acceleration targets for parts of their code and specify shareability and sensitivity of their program. Compilers need to generate versatile code, maybe several versions to target a range of devices at any given time. Additionally, in line with the approximate computing paradigm, one should be able to borrow compute capability from older or unreliable hardware.

Economical computing paradigm will minimize the pervasive waste of compute resources throughout the stack; it allows us to get more by utilizing what we already have.
```