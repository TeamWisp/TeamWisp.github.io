---
layout: post
title:  "Writing Proposals"
date:   2018-09-27 11:43:16 +0200
category: common
weight: 8
permalink: /proposals/
---

## The Goal

The goal is to set clear research guidelines and allow any team member to implement a proposal. The format below is written to force the author to truely think about how the feature would work and would be implemented into the renderer.

## The Format

```markdown
# Proposal

**Date:** [Day/Month/Year]
**Author:** [Author]

## Introduction

What is the task and how does it work.

## Approaches

Discuss different approaches and compare them. Than propose what we should do.

## Result

The expected end result when the entire proposal has been implemented.

## Workload

### Subtask 1

What is this subtask? How does it work?
A subtask could be a optimization technique like reprojection
or maybe its something like rendering a texture.

#### How long will it take

How long with this take and why.

#### What is the resut of this subtask

expected performance impact?
expected visual changes?

#### What files need to be modified.

* d3d12_xxx.cpp
* demo.hpp

### Subtask 2

// ... etc
```
