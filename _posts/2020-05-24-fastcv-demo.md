---
layout: post
published: true
title: 'FastCV: Hardware Acceleration of Computer Vision'
date: '2020-05-24'
---
[Qualcomm's FastCV SDK](https://developer.qualcomm.com/software/fast-cv-sdk) promises hardware acceleration of computer vision applications, providing a slew of APIs for common signal processing tasks (e.g., filters) without ties to particular hardware.

Personally I'm interested in the behaviour of hardware accelerated software on mobile systems; this makes FastCV seem like an excellent research platform. However, open-source applications that make use of the framework are far in between. Qualcomm ships two demo Android applications: *fastcvdemo* amd *fastcvcorner*. Below are the not-so-straightforward steps to build these applications on a Pixel 3 (despite the decade old official documentation).