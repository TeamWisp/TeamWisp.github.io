---
layout: post
title:  "Frame Interpolation"
category: common
---

Frame interpolation is a technique used to estimate the next frame from previous frames, a different name for this is “Motion interpolation” / “Motion correction”.

## How will it work with raytracing
Because raytracing has a low fps, this technique could help (at least) double the fps. This allows us to fake the idea that our demo runs at a high fps. As long as our demo scene doesn’t show things that cause artifacts, it should compliment raytracing.

## How is it implemented
Frame extrapolation can be implemented through AI; you use the previous frames to generate the next frame, and you render every other frame. Frame interpolation requires you to be behind every frame. Extrapolation can also use the motion vectors to determine what the movement of every pixel should be in an intermediate frame, also allowing to having the option of quadrupling your fps. But we probably have to use motion vectors even if we go for frame interpolation.

## Why don’t more games/engines use it?
This technique is mostly used for movies and TVs that have high refresh rates (120, 240 Hz, etc). Most movies have natural scenes (or frames that can be estimated easier), while games can have frames that are unpredictable (such as bullets, explosions, fast camera rotations, etc.). This can also create tears or glitches for a few frames, reducing the quality, but increasing the smoothness. You’re also behind a frame, which creates some input lag. However, for a walking simulator with restricted camera rotation speed, it should have minimal artifacts.

## Links

[Motion interpolation](https://en.wikipedia.org/wiki/High-definition_television) [0]

[Asynchronous spacewarp](https://developer.oculus.com/blog/asynchronous-spacewarp/) [1]

[Motion compensation (video)](https://www.youtube.com/watch?v=0Y5pTSz3pdM) [2]
