---
layout: post
title:  "Setting Up Your Workspace"
category: common
permalink: /workspace_setup/
weight: 0
---

## Prerequisites

* Git
* Visual Studio 15 2017
* [Static Code Analyzer]({{site.url}}/code_standards/#automated-code-analysis) *(or MSVC's `/analyze`)*
* FMOD *(Optional)*

## Installation

1. `git clone https://github.com/TeamWisp/Procedural-Ray-Tracing.git`
1. Run `install.sh`
1. The project files are located in `build_[compiler]_[platform]`

## Frequently Asked Questions

* ***Visual Studio doesn't detect new files*** - Automatic refreshing might not work for some people. Run the `reload.sh` file than Visual Studio will prompt you to reload the solution.
* ***Visual Studio places new files in the wrong place*** - Just create them manually from the file explorer.
