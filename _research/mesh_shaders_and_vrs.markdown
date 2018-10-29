---
layout: post
title:  "Mesh Shaders, TSS &#38; VRS"
category: renderer
---

## Mesh Shaders
### What is it?
Turing brings the compute pipeline into the graphics pipeline in the form of mesh shaders. Mesh shaders can cooperatively generate compact meshes (meshlets). This is done directly on the chip for consumption by the rasterizer.

The mesh shading pipeline replaces  Vertex and hull shaders with a task shader. And the domain and geometry shader are replaced with the mesh shader. The pixel shader remains unchanged. (This pipeline is optional)

![](../../images/ms_pipeline.webp)

* **Task Shader:** A programmable unit that operates in worksgroups and allows each to emit (or not) mesh shader workgroups
* **Mesh Shader:** a programmable unit that operates in workgroups and allows each to generate primitives

The mesh shader pipeline provides a number of benefits:

Higher Scalability through shader units by reducing fixed-function impact in primitive processing. The generic purpose use of modern GPUs helps a greater variety of applications to add more cores and improve shader’s generic memory and arithmetic performance.
Bandwidth-reduction, as de-duplication of vertices (vertex re-use) can be done upfront, and reused over many frames. The current API model means the index buffers have to be scanned by the hardware every time. Larger meshlets mean higher vertex re-use, also lowering bandwidth requirements. Furthermore developers can come up with their own compression or procedural generation schemes.
The optional expansion/filtering via task shaders allows to skip fetching more data entirely.
Flexibility in defining the mesh topology and creating graphics work. The previous tessellation shaders were limited to fixed tessellation patterns while geometry shaders suffered from an inefficient threading, unfriendly programming model which created triangle strips per-thread.

# What can we do with it?

* Isosurface extraction
* Finer Grained LOD
* Marching Cubes
* Tetrahedra Rendering
* Volumetric Rendering
* Dynamically triangulate meshes. **(Applicable to modelling software)**
* Dynamically insetting/outsetting geometry for visualizations **(Applicable to modelling software)**
* Geometry clipmaps and geomorphing
* Particles
* Glyphs
* Proxy Objects
* Point Clouds
* Procedural Electric Engineering Layouts
* Ribbons and trails
* Path rendering (For example for a minimap)
* Render using dynamic LOD’s except for when a object is selected. **(Applicable to modelling software)**
* Render using dynamic LOD's except for a area around the cursor. **(Applicable to modelling software)**

## Variable Rate Shading

### What is it?
According to NVIDIA it is a easy to implement rendering technique aimed at increasing rendering performance and quality by applying varying amount of processing power to different areas of the image. It does this by rendering a 2x1, 1x2 2x2 and etc till 4x4 clusters of pixels using the same “pixel shader pass”. This means that this technique can be used to reduce the cost of heavy pixel shaders.

### What can we do with it?
* Supersampling
* Foveated rendering (Lens based)
* Foveated rendering (Eye tracking)
* Optimize far-away terrain.
* Importance based GI
* Importance based AO
* Render entire scene clustered instead of the selected object. **(Applicable to modelling software)**
* SRV render everything if motion blur gets applied to it.
* Accelerate DoF by SRV rendering the blurred part.
* Render fog in clusters since you don’t need detail in fog.
* SRV render surfaces with little detail or very smooth surfaces.
* SRV render the floor and roof since no one ever looks up or down.

## Texture Space Shading

### What is it?

With Texture Space Shading (TSS), objects are shaded in a private coordinate space (a texture space) that is saved to memory, and pixel shaders sample from that space rather than evaluating results directly. With the ability to cache shading results in memory and reuse/resample them, developers can eliminate duplicate shading work or use different sampling approaches that improve quality.

![Texture Space Shading Visualization](../../images/tss.png)

TSS works very well with MIPS. When shading for a pixel, the developer can adjust the mapping into texture space, which MIP level (level of detail) is selected, and consequently exert fine control over shading rate. Because texels at low levels of detail are larger, they cover larger parts of an object and possibly multiple pixels. This means you can achieve VRS with TSS but it isn't as easy to implement nor as efficient. However it does give more flexibility.

In theory this technique could be used before Turing. Bud Turing makes it fast enough for it to be viable.

### What can we do with it?

Because when using normal rasterization a lot of the fragments don't have to be sampled due to culling. This isn't the case for TSS. This makes TSS only really viable for stereo/panorama rendering.

* Efficient stereo rendering.
* Mesh based Variable Rate Shading

### Multi View Rendering

### What is it?

The name already gives it away but Multi View Rendering is used to efficiently draw a scene to multiple viewports even if the origin or direction isn't the same. Turing architecture supports four views while at a API level you can have 32 views. Turing optimally processes triangles and their associated vertex attributes while rendering multiple versions. When accessed via the D3D12 View Instancing API, the developer simply uses the variable SV_ViewID to index different transformation matrices, reference different blend weights, or control any shader behavior they like, that varies depending on which view they are processing.

### What can we do with it?

* Virtual Reality
* large FOV devices (see image)

![200 Degree HMD Where Two Canted Panels are Used and Benefit from MVR](../../images/mvr.png)

## Further reading

* [http://hhoppe.com/proj/vdrpm/](http://hhoppe.com/proj/vdrpm/)
* [https://developer.nvidia.com/gpugems/GPUGems2/gpugems2_chapter02.html](https://developer.nvidia.com/gpugems/GPUGems2/gpugems2_chapter02.html)
* [http://reedbeta.com/blog/mesh-shader-possibilities/](http://reedbeta.com/blog/mesh-shader-possibilities/)
* [https://devblogs.nvidia.com/introduction-turing-mesh-shaders/](https://devblogs.nvidia.com/introduction-turing-mesh-shaders/)
* [https://perso.telecom-paristech.fr/boubek/papers/FPBGI/FPBGI_lowres.pdf](https://perso.telecom-paristech.fr/boubek/papers/FPBGI/FPBGI_lowres.pdf)
* [https://www.researchgate.net/publication/262164168_A_Significance_Cache_for_Accelerating_Global_Illumination]([https://www.researchgate.net/publication/262164168_A_Significance_Cache_for_Accelerating_Global_Illumination)
* [https://graphics.pixar.com/library/PointBasedGlobalIlluminationForMovieProduction/paper.pdf](https://graphics.pixar.com/library/PointBasedGlobalIlluminationForMovieProduction/paper.pdf)
* [https://developer.nvidia.com/vrworks/graphics/variablerateshading](https://developer.nvidia.com/vrworks/graphics/variablerateshading)
* [https://devblogs.nvidia.com/turing-variable-rate-shading-vrworks/](https://devblogs.nvidia.com/turing-variable-rate-shading-vrworks/)
* [https://www.tomshardware.com/reviews/nvidia-turing-gpu-architecture-explored,5801-9.html](https://www.tomshardware.com/reviews/nvidia-turing-gpu-architecture-explored,5801-9.html)
* [https://www.youtube.com/watch?v=LCT7pF6HrIg](https://www.youtube.com/watch?v=LCT7pF6HrIg)
* [http://on-demand.gputechconf.com/siggraph/2018/video/sig1811-3-christoph-kubisch-mesh-shaders.html](http://on-demand.gputechconf.com/siggraph/2018/video/sig1811-3-christoph-kubisch-mesh-shaders.html)
* [https://www.youtube.com/watch?v=Rpy0-q0TyB0](https://www.youtube.com/watch?v=Rpy0-q0TyB0)
* [https://devblogs.nvidia.com/texture-space-shading/](https://devblogs.nvidia.com/texture-space-shading/)
* [https://devblogs.nvidia.com/nvidia-turing-architecture-in-depth/](https://devblogs.nvidia.com/nvidia-turing-architecture-in-depth/)
* [https://www.nvidia.com/content/dam/en-zz/Solutions/design-visualization/technologies/turing-architecture/NVIDIA-Turing-Architecture-Whitepaper.pdf](https://www.nvidia.com/content/dam/en-zz/Solutions/design-visualization/technologies/turing-architecture/NVIDIA-Turing-Architecture-Whitepaper.pdf)






