---
layout: post 
title: "Detailed Feature List"
category: renderer
permalink: /feature_list/
---

This is a list of all the features. This document is a reference for when we are planning and dividing work.

## High Level Features

**Cirtical Features:**

* Deferred Rendering
* Skybox Rendering
* Model Loading
	* FBX
	* FBX Materials
* Texture Loading
	* HDR
	* PNG
	* DDS
* Frame Graph
* Scene Graph
* GPU Resource Management
* DXR Raytracing
	* Reflections
	* Shadows
	* Translucency
	* Global Illumination
	* Ambient Occlusion
* Physically Based Rendering
* Acceleration Structure Generation From Scene Graph
* Maya Viewport Plugin
* Maya Scene Graph Conversion To Wisp Scene Graph
* ImGui
	* Standalone

**Non-Cirtical Features:**

* SRV Accelerate everything but the selected object.
* Mesh Shader LOD generation.
* HDR Rendering
* Deep learning de-noising
* DLSS
* Signed Distance Functions
	* Raytracing SDF's
	* SDF Maya Editor
* ImGui
	* Maya Viewport
