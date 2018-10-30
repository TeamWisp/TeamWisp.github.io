---
layout: post
title:  "Temporal Accumulation"
category: research 
---

## Definition

Temporal accumulation is when you use the input of the previous frame(s) to determine if there are any artifacts in the current frame, so you can correct those.

“As in TAA, we require a 2D motion vector associated with each color sample Ci for frame i. This describes geometric motion from the prior frame, and allows us to backproject Ci to its screen space location in the prior frame. By accessing a color history buffer, output by our filter in the prior frame, we can continuously accumulate color samples over multiple frames. For each Ci we backproject to find sample Ci−1 from the color history buffer, and compare the two samples’ depths, object-space normals, and mesh IDs to determine if they are consistent (i.e., on the same surface)”

## Use

AFAIK, if we have DLSS (or a denoiser) we don’t really need this. DLSS provides upscaling (so we don’t render every other pixel, we just render lower quality) and a denoiser does all of this better (using AI). This is because temporal accumulation, spatial reconstruction and bilateral cleanup are all used to ‘scale up’ the half-res output from the raytracer. DLSS is faster and gives better results. But without DLSS it provides you with a way to get rid of some of the artifacts caused by reconstructing a full frame (using spatial reconstruction). DLSS also doesn’t require you to use input of previous frames (directly), meaning that there can’t be any interference from previous frames (that also cause artifacts; like lights that flicker for example).

## Estimation

I’d estimate that figuring out how TA works is extremely difficult, because there’s almost no resources on it (especially code). The only resources are mathematical papers, which are hard to convert into code (since you have to understand formulas and the theory behind them). Implementing this could easily take between 4 to 8 weeks at least.

## Implementation

You need the estimation of previous frame and the current frame. You lerp this using an alpha variable;

$$
C'\tiny{i}\normalsize{} = a \times C\tiny{i}\normalsize{} + (1 - a) \times C'\tiny{i - 1};
$$

Ci = color at frame i
C’i = interpolated/final color at frame i
Ci-1 = backprojected color at previous frame (i - 1)
C’i-1 = exponential moving average (EMA) of Ci-1
a = temporal fade (trading temporal stability for lag); a = 0.2 (works best)

This gives us the following pseudocode:

```cpp
foreach pixloc in pixels
	curr = current[pixloc]
	current[pixloc] = 0.2 * curr + 0.8 * ema(resample(curr));
```

‘Resample’ goes back in time 1 sample by using the motion vector image to estimate where the pixel should have been previous frame. It also uses a 2x2 tap bilinear filter to improve image quality. If a tap contains inconsistent geometry, a 3x3 filter is used (for thin geometry such as foliage). If the geometry is still inconsistent, the `C'i = Ci. (ema(resample(curr)) = curr)`. 


## References

* [Presentation](https://youtu.be/MyTOGHqyquU?t=1101)
* [Adaptive temporal filtering](https://cg.ivd.kit.edu/publications/2018/adaptive_temporal_filtering/adaptive_temporal_filtering.pdf)
* [Temporal accumulation](https://epubs.surrey.ac.uk/39622/2/accumulation.pdf)
* [NVIDIA SVGF](https://www.highperformancegraphics.org/wp-content/uploads/2017/Papers-Session1/HPG2017_SpatiotemporalVarianceGuidedFiltering.pdf)
* [Temporal filtering 4.1](https://cg.ivd.kit.edu/publications/2017/svgf/svgf_preprint.pdf)
