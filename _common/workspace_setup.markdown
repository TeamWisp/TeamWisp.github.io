---
layout: post
title:  "Setting Up Your Workspace"
category: common
permalink: /workspace_setup/
weight: 0
---

## Prerequisites

* Git
* Windows 10 (1803 or higher)
* Visual Studio 15.9
* CMake 3.12.4 or higher
* Windows SDK 10.0.17763.0
* Maya 2018
* Feature Level 12.1 Capable Hardware
* [Static Code Analyzer]({{site.url}}/code_standards/#automated-code-analysis) *(or MSVC's `/analyze`)*

## Installation

1. Open CMD or Powershell in the installation directory of your choice. 
1. `git clone https://github.com/TeamWisp/Procedural-Ray-Tracing.git` (Clones the repository in a foler named Procedural-Ray-Tracing. To specify a different directory add a space to the end of the command followed by your prefered path)
1. Run `install.sh`
1. The project files are located in `build_[compiler]_[platform]`

**Optional:**
1. To create a feature branch call `git checkout -b feature_[yourfeaturename]`.

## Frequently Asked Questions

* ***Visual Studio doesn't detect new files*** - Automatic refreshing might not work for some people. Run the `reload.sh` file than Visual Studio will prompt you to reload the solution.
* ***Visual Studio places new files in the wrong place*** - Just create them manually from the file explorer.
