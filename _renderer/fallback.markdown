---
layout: post
title:  "How To Use Fallback"
category: renderer
permalink: /fallback/
---

## Requirements

Your machine requires resource binding tier 3. Without this you can't run the project.

## Steps

1. Reduce the ray recursion depth to a maximum of 1. (In the shader and the pipelines)
2. For Hybrid only: Disable the shadow ray cast.
3. To reduce rendering artifacts render a scene with only a single mesh at the center.
