---
layout: post
title:  "Scene Graph"
category: renderer
---

## **Foreword**

The goal of this document is to take a deeper look at scene graphs and their composition.

## **Scene graphs**

### **What is a scene graph**

A scene graph is a general data structure which arranges the logical and often spatial representation of a graphical scene. Scene graphs are useful for modern games using 3D graphics and increasingly large worlds or levels. In such applications, nodes in a scene graph (generally) represent entities or objects in the scene.

![](../../images/sg/image_0.png)

Image source: [[3]](https://wiki.jmonkeyengine.org/jme3/the_scene_graph.html)

### **Scene graphs in Game Engines**

In game engines scene graphs provide an efficient way to traverse the transformation matrices for game entities. The scene graph traverses its lists and sends each game object to an Engine Loop. Where it gets rendered, and its coordinate space gets updated.

Aside from its fast traversal property, a scenegraph provides a clean method to transform a child node transform with respect to its parent node transform.

Spatial data structures help optimize the sorting process and provide a more efficient traversal path for the game engine. 

### **Spatial Data Structures**

Space-partitioning systems are often hierarchical, meaning that a space (or a region of space) is divided into several regions, and then the same space-partitioning system is recursively applied to each of the regions. The regions can be organized into a tree, called a space-partitioning tree.[[1]](https://en.wikipedia.org/wiki/Space_partitioning)

An effective way of combining spatial partitioning and scene graphs is by creating a scene leaf node that contains the spatial partitioning data [[0]](https://en.wikipedia.org/wiki/Scene_graph)

#### BSP Tree (Binary Space Partition)

BSP-Trees are optimal for static geometry. This is because BSP-Trees are generally slow to build. The reason for this is because they use non-axis-aligned planes, which take longer to optimally find because they subdivide geometry on axis boundaries, assuring no objects cross split planes. Axis aligned implementations exist but are beyond the scope of this research document.

![](../../images/sg/image_1.png)

#### BVH Tree (Bounding Volume Hierarchy)

BVH Trees are beneficial if the data is three dimensional/non-static and clustered in 3D space.

This makes them less performant than BSP Trees but are quicker to build, and easier to change for dynamic objects.

Image source: [[5]](https://en.wikipedia.org/wiki/Bounding_volume_hierarchy)

#### BSP vs BVH 

Scenes with lots of instances of simpler and similar geometry (houses, trees, asteroids, etc) will often use a BVH, because putting the geometry into a BSP-tree would mean duplicating and splitting the geometry for every instance.

#### Octree and Quadtree

Scene graphs for dense objects such as height fields and polygon meshes may be better of to use octrees, which are specialized variants of a 3D bounding box hierarchy. 

A quadtree is the 2D equivalent of a octree.

## Research questions & answers

* **How can we integrate it into the engine (Risk estimation)?** 
If we end up using an Entity Component System (ECS) we will want to map components to a scene graph. We can do this by referencing the scene graph inside a component, for example a mesh renderer, then a system will handle the node transformations as it processes the components. When the component is destroyed it’s scene graph equivalent is also removed. This makes the ECS a user facing interface whilst the scene graph is maintained under the hood. Doing it this way we have the best of both worlds. However, extra care is to be taken when using a data oriented entity component system. Issues may arise with the data layout and cache coherency if not adequately sorted or accessed. If an ECS is undesirable a common scene graph implementation will suffice and eliminate the risk of making both concept work in union.

* **Is the way the user builds the scene structure affected by the types of scene structures?**
No, from the user perspective the process doesn’t change. Under the hood objects marked as static may be sorted using BSP-Trees whereas dynamic objects will use BVH. A Scene/World node will be provided by default and will act as the main access point to our scene graph.

* **How does it compliment ray-tracing?** 
Space partitioning is particularly important in computer graphics and is especially helpful for ray tracing, where it is frequently used to organize the objects in a virtual scene. A typical scene may contain millions of polygons. Performing a ray/polygon intersection test with each would be a very computationally expensive task.[[1]](https://en.wikipedia.org/wiki/Space_partitioning) Luckily for us, Nvidia provides this optimization out of the box when using the RTX SDK. This will likely always be a optimization for the vendor rather than the user, unless of course your goal is to make your own. 

## Conclusion

A Scene graph is a general data structure. Which one works best depends on the use case.

This is why this document had a brief look at the general scene graph concept and four different kinds of spatial data structures to accompany it.

From this research I conclude that there is no one-fits all structure we can use, and that we will likely want to have at least a BSP and BVH tree for the Wisp project for the reasons discussed in the QA section. 

## Sources & Reading material

[[0] Scene Graph - Wikipedia](https://en.wikipedia.org/wiki/Scene_graph)

[[1] Space Partitioning - Wikipedia](https://en.wikipedia.org/wiki/Space_partitioning)

[[2] Spatial Data Structures and Speed-Up Techniques by Ulf Assarsson](http://www.cse.chalmers.se/edu/course/TDA362/spatial.pdf)

[[6] Binary Space Partitioning Trees by Bruce F. Naylor](https://pdfs.semanticscholar.org/c496/61c65c1780053dcc1ccd71abec5f244af2c9.pdf)

[[7] Bounding volume hierarchy - Wikipedia](https://en.wikipedia.org/wiki/Bounding_volume_hierarchy)

[[8] Cache‐Efficient Layouts of Bounding Volume Hierarchies by Sung‐Eui Yoon and Dinesh Manocha](https://onlinelibrary.wiley.com/doi/full/10.1111/j.1467-8659.2006.00970.x)

[[10] Space Partitioning - Bounding Volume Hierarchy - Youtube](https://www.youtube.com/watch?v=Xh02eHXkGOY)
