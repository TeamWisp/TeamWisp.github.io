---
layout: post
title:  "TAA-like clip"
category: research 
---

**How long will it take to implement:** 1 day

## What is it

It is a method to fix the ghosting that occurs with screenspace temporal accumulation techniques. It works by comparing and modifying the values from the previous frames to match the current frame, eliminating most of the more severe ghosting effects.

## What can we use it for

Since for raytracing most parts require the use of screenspace temporal accumulation to enable our application to run in real time we’d get a lot of ghosting whenever our camera or something in the scene moves. By using this technique we can get rid of most of these effects, creating a massive improvement in visual quality.

## How does it work

The basic version, neighborhood clipping, works by taking a kernel of pixels (the paper uses 4x4), and building a convex hull around the color values of these pixels, in a way that every pixel in the kernel falls inside of the convex hull. By then projecting the pixel from the previous frame into this same space you can see if they fall inside the convex hull. If they don’t you create a segment between the old color and the new color. By then intersecting the segment with the convex hull you can get a corrected color value, essentially creating a way to reset the temporal accumulation if data becomes invalid.
An issue here is that actually constructing the convex hull will take time. A faster method is to use a AABB instead. The issue here is that an AABB large enough to contain all color values will cause issues because the intersection point can be to far away from the convex hull, meaning that you’ll still get ghosting. In the paper they fixed this by using variance estimation to calculate smaller AABB that approximates the average color much better.

For this, they use the following formulas.

$$
m1 = \sum\limits_{i=0}^n c\tiny{i}
$$

$$
m2 = \sum\limits_{i=0}^n c\tiny{i}\normalsize{} \times c\tiny{i}
$$

$$
\mu = m1 / N
$$

$$
o = \sqrt{m2 / N - \mu \times \mu}
$$

$$
minc = \mu - y \times o
$$

$$
maxc = \mu + y \times o
$$

Here, $N$ is the number of color samples in the kernel, $c\tiny{i}$ is color sample i, $y$ is a scaling factor applied to the size of the AABB, with larger gammas being more stable, but causing more ghosting. When gamma is too small accumulation stops. $minc$ and $maxc$ are the corners of the AABB. After this, we can also clamp the AABB against the original bounding box, so we don’t get anything larger than the required minimum.

[http://developer.download.nvidia.com/gameworks/events/GDC2016/msalvi_temporal_supersampling.pdf](http://developer.download.nvidia.com/gameworks/events/GDC2016/msalvi_temporal_supersampling.pdf)


