---
date created: 2023-01-08
date modified: 2023-01-08
tags: [linux,os,cs]
title: 📖 HOW LINUX WORKS
---
>✒ By Satoru Takeuchi ([Github](https://github.com/satoru-takeuchi), [Twitter](https://twitter.com/satoru_takeuchi), [Intro](https://cybozu.co.jp/recruit/staff/sat.html))

## CHAPTER 1: Overview of computer systems

When computer running, the hardware device will repeat the following process:
1. Request / Input by devices or networking.
2. Read the program from RAM, execute on CPU, save results in RAM (part for save result).
3. Save results to RAM or HDD/SSD storage, or send throw network devices, or output to device.
4. repeat 1 ⬆️

These meaningful steps for user, we call that **the program**. There r Applications, middlewares, and OS program for users.

When program is running, we call that **the process**: the unit of program running on OS, one program not only have one processes.

When program need call devices, we need driver. Avoid each program making a separate implementation for each device. the same devices types, they have the same interface design on OS driver.

**KERNEL MODE** and **USER MODE**: for isolation, the processes cannot access the device directly, these mode exist implementation in the CPU. A program that runs in kernel mode and is not called by normal user processes is called a **kernel**.

**SYSTEM CALL** : the process call the devices driver (or kernel features).

Kernel's main features:
- Processes management (scheduler);
- Memeory management;
- File system features;
- System call API;
- Devices driver;

## CHAPTER 2: USER MODE FEATURES
