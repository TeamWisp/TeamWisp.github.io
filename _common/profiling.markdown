---
layout: post
title:  "Profiling Plan"
permalink: /profiling/
weight: 50
---

Every build the build system runs a set of tests which meassure performance. If there is a massive drop in performance a message will be send to Discord.

In-depth manual profiling happens at the end of every block where production stagnates anyway. The profiling is performed by a group of about 3 people who are proficient at profiling. Their task is to find any poorly performing systems and devised a action plan on how to make said system more performant. We don't have a standardized profiling application. Use whatever you are most proficient in.

## GPU Profilers

* Render Doc
* NVIDIA Nsight
* Visual Studio Profiler & Debugger

## CPU Profilers

* VTune
* Visual Studio Profiler

## Memory Profilers

* Visual Studio Profiler
* Valgrind
* Dr.Memory
