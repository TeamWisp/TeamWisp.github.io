---
layout: post
title:  "Working with Maya scene data"
category: research
---

# ![](../../images/maya/image_0.jpg)

## Foreword
### Purpose of this document

As a team, we want to create a rendering library that allows users to just plug in the files and have a renderer. We aim to make this progress as smooth as implementing, for example, Dear ImGui.

Due to the stand-alone nature of our project, we are thinking about writing a Maya plugin that adds real-time rendering to the viewport. This document aims to give a clear overview of what data Maya can supply us with, how it is structured, and how it can be used.

### Previous research

The last time our team did research into Maya and its API, we decided to look into replacing the Maya render view window and / or the Maya viewport. That research did not focus on learning more about the Maya scene data and how it can be extracted.

### Research questions

* How does Maya structure its scene?
	* Node inheritance.
	* Texture assignment.
	* Names and IDs.
* Can scene nodes be easily accessed from code?
	* Retrieve geometry.
	* Read textures.
	* Interpret shader parameter information.
* Does a renderer get access to textures and the like?
	* File format.
* What about shaders?
* How do nodes work?


## Scene structure

### Introduction

Maya makes use of a scene architecture called “directed acyclic graph” (“DAG” in short). It is organized as a tree of transform nodes and shape nodes. This section of the document aims to provide the reader with a basic overview of the DAG tree in Maya.

### DAG node

The base of every node in the DAG is the “DagNode” class. In its simplest form, is just an entry in the DAG tree and may have parents, siblings and / or children. It does not necessarily know about transformations or geometry.

The transform and shape nodes are both derived from the “DagNode”.

### DAG paths

A path through the DAG is represented as a string, for example: “|root|child|child_of_child”. The pipe symbol is used to indicate the beginning of a node.

The only way to make sure that you are accessing the correct node is by using its DAG path. There are cases where there are multiple DAG paths to the same node (when instancing), so the unique DAG path should be used to access a node.

The Maya DAG allows node names to be reused, as long as the nodes do not share a common parent.

### Transform nodes

Maintains transform data such as position, rotation, and scale. In addition to this data, the transform nodes also contain parenting information (parent-child relationship, rotate the base, rotate all children).

### Shape nodes

Geometry data is stored in these nodes. They do not contain parenting information or transform information.

When an artist creates an object, for example, a sphere, the **transform** node will store the transform data as well as a **shape node as its child**. Child nodes are “grouped” beneath the transform node. This allows them to share transformation information and be treated as a unit.

Since a transform node cannot contain any geometry data, geometry always needs **two** DAG nodes above it: a shape node above the geometry (holds the data), and a transform node directly above the shape node (to maintain the transform).

This is the simplest use case, as transform nodes can have multiple children nodes without some of the children being direct children of the parent node. Please refer to the [instancing](#Instancing) and [generalized instancing](#Generalized-instancing) chapters for more information.

### The underworld

A shape node has a parameter space. This can hold data such as the UV space of a Non-uniform rational B-spline (NURBS) surface. In fact, the parameter space of a shape node could even hold a subgraph of nodes.

The `->` symbol is used in the DAG path to indicate the transition from the shape node to the root node of the underworld path. The DAG path to a node with extra data in its underworld space, could look something like this: `|root|child|child_of_child->underworld_root|child`. Note the `->` used to indicate the underworld path of the node.

## Instancing

Both transform as well as shape nodes can have multiple parents. These nodes are “instanced”. Instancing is used to reduce the amount of geometry of a model.

The Maya API documentation uses the creation of a tree as an example. Say you have a tree that needs a few thousand leaves. It is possible to create a unique model for each and every leaf, but it is more efficient to create a single leaf “instance” and “paste” it all over the tree.

![](../../images/maya/image_1.png)

Shown above is an image from the API documentation that illustrates what multi-parent inheritance looks like. The leaf mesh is instanced on transform 1 and 2, since they both have the transform node and shape node of the leaf as their child.

### Generalized instancing

Maya supports "generalized instancing". This means that a node can instance another node. The node that is instanced does not have to be a sibling.

![](../../images/maya/image_2.png)

The image above shows how generalized instancing works. Node 2 and node 4 both instance node 3, however, they are not siblings. This type of instancing can only happen as long as the reference is not made back up the hierarchy. If this would have been allowed, it would break the acyclic nature of the DAG (circular referencing could occur).

## Interfacing with the DAG

### Introduction

Now that we have a better understanding of the DAG and how it works, it is time to try out some samples. The main focus of this section will be on extracting data from the DAG. The main focus will be on extracting vertex data, transform data, and the texture data.


### Extracting information

The Autodesk API documentation shows a DAG-walking example that can be expanded upon. It is recommended to use their code sample as a starting point for most applications that need to iterate through the DAG.

#### Traversing the DAG

Traversing the DAG can be done in two ways: breadth-first or depth-first. Note that breadth-first searching can be significantly slower than depth-first searching when traversing whole hierarchies (source: Maya API documentation).

Autodesk added a method to the Maya API that allows developers to easily traverse the DAG tree breadth- or depth-first. By specifying a search flag, the API will take care of iterating over the tree.

Please note that the code samples in this chapter do not have any error checking to keep things concise and to the point. In a real-world application, the error status should be checked after the operations that write to the status variable.

```cpp
// Create the iterator
MItDag dagIterator( traversalType, filter, &status);

// Traverse the DAG
for ( ; !dagIterator.isDone(); dagIterator.next() )
{
	// Do things with the DAG nodes in here...
}
```

#### Accessing a DAG node

In the [DAG paths](#DAG-paths) chapter, we briefly touched upon the use of the DAG paths and why they should be used to retrieve nodes. This paragraph will demonstrate how the Maya C++ API can be used to get the DAG path of the current node in the tree.

```cpp
// Try to retrieve the DAG path
dagIterator.getPath(dagPath);

// Retrieve the DAG node using the DAG path
MFnDagNode dagNode(dagPath, &status);
```

#### Node-specific data

In the [DAG node](#DAG-node) chapter, we learned about the base node class of the Maya DAG. This node is unable to hold any meaningful data for our renderer, besides its relative “position” in the DAG tree.

The DAG path can be queried for specialized DAG nodes (children of the base node), such as camera, lights, or NURBS surfaces:

```cpp
if (dagPath.hasFn(MFn::kCamera))
	// Found a camera node, camera-specific data can now be queried
else if (dagPath.hasFn(MFn::kLight))
	// Found a light node, light-specific data can now be queried
else if (dagPath.hasFn(MFn::kNurbsSurface))
	// Found a NURBS surface node, NURBS-specific data can be be queried
else if (dagPath.hasFn(MFn::kMesh))
	// Found a mesh node, mesh-specific data can be queried
```

### Texture Data

Textures in Maya are stored as file texture nodes. These are connected to a texture placement node, which, in turn, connects to a color channel on a material node.

#### Texture file paths

Iterating through textures is the same as iterating through transforms. The constructor of the iterator can be used to set a “filter”. When this filter is set to texture files, the DAG iterator will only return textures.

```cpp
// Create an iterator that goes through all transforms in the scene using the file texture filter
MItDependencyNodes it(MFn::kFileTexture);
while (!it.isDone()) {
	// Get the attribute for the full texture path
	MPlug ftn = fn.findPlug("ftn");
	// Get the file name
	ftn.getValue(filename);
	it.next();
}
```

#### Texture placement nodes

Maya has texture placement nodes. These nodes are used to scale, rotate, and translate the texture on a geometry surface. These nodes could be needed when rendering things such as UV-animated textures. Just like when working with file paths, there are no standard functions to get the texture placement nodes, so plugs will have to be used once again.

```cpp
// Get the texture coverage in U and V
MPlug plug = fn.findPlug("cu");
plug.getValue(f);		      // Now stores the coverage U component
plug = fn.findPlug("cv");	// Now stores the coverage V component
plug.getValue(f);
```

#### Material data

Maya supports polygon editing and NURBS editing. As a result, the materials of those model “types” also need to be access in slightly different ways. The code is the same, however, a NURBS surface requires the application to iterate slightly differently over the DAG tree.

Every material in Maya derives from the Lambert material type, so applying that as a filter to the DAG node iterator will return all materials in the scene. Once the iterator returns a material, the “API type” can be queried for a more specific material type such as Phong, or Blinn Phong.

To keep the code sample concise, the “OutputColor” function has been omitted. It is a really simple function that only queries the plugs and returns the red, green, blue, and alpha values of the property.

```cpp
MItDependencyNodes it(MFn::kLambert);
while (!it.isDone()) {
	// Add more material types when needed
	switch (it.item().apiType()) {
    	case MFn::kPhong:
        	MFnPhongShader fn(itDep.item());
        	OutputColor(fn, "ambientColor");
        	OutputColor(fn, "color");
        	OutputColor(fn, "specularColor");
        	OutputColor(fn, "incandescence");
        	OutputColor(fn, "transparency");
        	break;
    	case MFn::kBlinn:
        	MFnBlinnShader fnBlinn(itDep.item());
        	cout << "Blinn " << fn.name().asChar() << "\n";
        	OutputColor(fn, "ambientColor");
        	OutputColor(fn, "color");
        	OutputColor(fn, "specularColor");
        	OutputColor(fn, "incandescence");
        	OutputColor(fn, "transparency");
        	Break;
	}
	it.next();
}
```

#### Mesh data
Probably one of the most important data structures in Maya. Mesh data contains vertices, indices, tangents, normals, and like. This data is required when building a custom rendering, so it is extremely important to understand how this data can be retrieved from the scene graph.

Every polygonal surface in Maya is of the type “mesh”. This makes it easy to find them in the scene, as the API can just iterate through the DAG using a mesh filter. This code will not be shown in this chapter, as it has been shown multiple times in this document already. Please refer to previous chapters when it comes to iterating the DAG and the usage of filters.

Besides omitting the iteration code, the samples below also do not include the attachment of function sets (referred to as “fn”), since that has been shown multiple times as well.

##### Vertex Data

```cpp
fn.getPoints(vertices);
for (int i = 0; i != vertices.length(); ++i) {
	// Access vertices like so:
	// vertices[i].x
	// vertices[i].y
	// vertices[i].z
}
```

##### Vertex Normal

```cpp
fn.getNormals(normals);
for (int i = 0; i != normals.length(); ++i) {
	// Access normals like so:
	// normals[i].x
	// normals[i].y
	// normals[i].z
}
```

##### Vertex texture coordinates

In Maya, a mesh can hold a varying number of texture coordinate sets. So there is the possibility of not having any texture coordinates, or having multiple texture coordinate sets. The Maya API does still return a set, even when there are none. As a workaround, the length of the first texture coordinate set should be checked first.

```cpp
// Check if the mesh holds any texture coordinate sets
if (!uvsets.length() || !fn.numUVs(uvsets[0]))
	return;
for (int j = 0; j != uvsets.length(); ++j) {
	// Get the UV coordinates
	fn.getUVs(u_coords, v_coords, & uvsets[i]);
	for (int i = 0; i != fn.numUVs(uvsets[i]); ++i) {
    		// Access texture coordinates like so:
    		// u_coords[i]
    		// v_coords[i]
	}
}
```

##### Faces / indices

Face data is a bit more tricky than the other types of mesh data we have seen thus far. To get access to the data, a mesh polygon iterator has to be used. Each face contains indices that refer to vertices, normals, and texture coordinate (sets).

If no texture coordinate sets exist, an attempt to index it will make the application crash. Therefore, it is important to check whether the first texture coordinate set is empty.

```cpp
// Special iterator used to iterate through the faces
MItMeshPolygon it(obj);
while (!it.isDone()) {
	int vertexCount = it.polygonVertexCount();
	for (int i = 0; i < vertexCount; ++i) {
    		// Access the indices like so:
    		// it.vertexIndex(i)
    		// it.normalIndex(i)
    		
		if (meshHasTexCoords) {
        		int uv_index;
        		
			for (int k = 0; k < sets.length(); ++k) {
            		it.getUVIndex(i, uvIndex, &sets[k]);
            		// Access the texture coordinate index like so:
            		// uvIndex
        		}
    		}
	}
}
```

##### Instances

One of the more complex concepts in Maya is instances. Instanced meshes can be recognized by looking at the parent count. Nodes that have more than one parent are instanced. The parent count also reveals how many instances of a mesh there are in the scene.

The “parent” function can be used to return the parent transforms of the instances. So, “parent(0)” returns the parent transform of the first instance, and “parent(1)” returns the parent transform of the second instance.

Since instances can use different shaders, the mesh functions can be used to retrieve the shader that is currently assigned to the mesh.

## Conclusion

The Maya DAG is fairly complex. Autodesk integrated a lot of features into Maya, which allows for really complex DAG hierarchies. Luckily, the API is really well documented, and it seems like all the data we need is readily available.

Based on the code samples, it seems that the API is capable of delivering all data we need for a renderer. The only thing we might have some issues with is the instancing of objects.

Maya has quite a complex DAG tree, so there could be some issues regarding the conversion between the Maya DAG tree and our own format.

## Risk analysis

\+ Well documented API.

\+ Lots of plugin samples that use the API.

\+ API is easy to use.

\+ Data from the scene is generic, it can be put into a structure of our own.

<br>

\- NURBS surfaces could pose a challenge.

\- Extremely complex DAG trees could be difficult to convert.

\- Implementing a Maya plugin properly will take quite some time.

## References

* (n.d.). Retrieved from http://help.autodesk.com/view/MAYAUL/2018/ENU/?guid=__files_DAG_Hierarchy_Generalized_instancing_htm
* (n.d.). Retrieved from http://help.autodesk.com/view/MAYAUL/2018/ENU/?guid=__files_DAG_Hierarchy_Nodes_htm
* (n.d.). Retrieved from http://help.autodesk.com/view/MAYAUL/2018/ENU/?guid=__files_API_Loading_a_Plugin_htm
* (n.d.). Retrieved from http://help.autodesk.com/view/MAYAUL/2018/ENU/?guid=__files_API_MStatus_class_htm
* (n.d.). Retrieved from http://help.autodesk.com/view/MAYAUL/2018/ENU/?guid=__files_GUID_13D86D23_65DB_49A2_BD9C_DB9EF9F8644A_htm
* (n.d.). Retrieved from http://help.autodesk.com/view/MAYAUL/2018/ENU/?guid=__files_Setting_up_your_build_environment_htm
* (n.d.). Retrieved from http://help.autodesk.com/view/MAYAUL/2018/ENU/?guid=__files_DAG_Hierarchy_Overview_of_the_DAG_Hierarchy_htm
* (n.d.). Retrieved from http://help.autodesk.com/view/MAYAUL/2018/ENU/?guid=__files_API_Naming_Conventions_htm
* (n.d.). Retrieved from https://nccastaff.bournemouth.ac.uk/jmacey/RobTheBloke/www/research/maya/mfnmesh.htm
* (n.d.). Retrieved from http://help.autodesk.com/view/MAYAUL/2018/ENU/?guid=__files_Dependency_graph_plugins_The_basics_htm
* (n.d.). Retrieved from http://help.autodesk.com/view/MAYAUL/2018/ENU/?guid=__files_API_Important_plugin_features_htm
* (n.d.). Retrieved from http://help.autodesk.com/view/MAYAUL/2018/ENU/?guid=__files_API_Adding_arguments_htm
* (n.d.). Retrieved from https://nccastaff.bournemouth.ac.uk/jmacey/RobTheBloke/www/research/maya/mfnmesh.htm
* (n.d.). Retrieved from http://help.autodesk.com/view/MAYAUL/2018/ENU/?guid=__files_API_MObject_htm
* (n.d.). Retrieved from http://help.autodesk.com/view/MAYAUL/2018/ENU/?guid=__files_API_Error_checking_htm
* (n.d.). Retrieved from http://help.autodesk.com/view/MAYAUL/2018/ENU/?guid=__files_Setting_up_your_build_env_Windows_env_32bit_and_64bit_htm
* (n.d.). Retrieved from http://help.autodesk.com/view/MAYAUL/2018/ENU/?guid=__files_DAG_Hierarchy_Transforms_with_multiple_shapes_htm
* (n.d.). Retrieved from https://nccastaff.bournemouth.ac.uk/jmacey/RobTheBloke/www/research/maya/mfntexture.htm
* (n.d.). Retrieved from https://nccastaff.bournemouth.ac.uk/jmacey/RobTheBloke/www/research/maya/mfnmaterial.htm
* (n.d.). Retrieved from http://help.autodesk.com/view/MAYAUL/2018/ENU/?guid=__files_API_Unloading_a_plugin_htm
* (n.d.). Retrieved from http://help.autodesk.com/view/MAYAUL/2018/ENU/?guid=__files_API_Writing_a_simple_plugin_htm
* (n.d.). Retrieved from http://help.autodesk.com/view/MAYAUL/2018/ENU/?guid=__files_API_Introduction_htm
* (n.d.). Retrieved from http://help.autodesk.com/view/MAYAUL/2018/ENU/?guid=__files_DAG_Hierarchy_htm
* (n.d.). Retrieved from http://help.autodesk.com/view/MAYAUL/2018/ENU/?guid=__files_API_Error_logging_htm
* (n.d.). Retrieved from https://nccastaff.bournemouth.ac.uk/jmacey/RobTheBloke/www/research/maya/mfnmesh.htm
* (n.d.). Retrieved from https://nccastaff.bournemouth.ac.uk/jmacey/RobTheBloke/www/research/maya/mfnmaterial.htm
* (n.d.). Retrieved from http://help.autodesk.com/view/MAYAUL/2018/ENU/?guid=__files_DAG_Hierarchy_Transforms_and_shapes_htm
* (n.d.). Retrieved from http://help.autodesk.com/view/MAYAUL/2018/ENU/?guid=__files_API_Interacting_with_Maya_htm
* (n.d.). Retrieved from http://help.autodesk.com/view/MAYAUL/2018/ENU/?guid=__files_API_Wrappers_htm
* (n.d.). Retrieved from http://help.autodesk.com/view/MAYAUL/2018/ENU/?guid=__files_API_Objects_and_Function_Sets_htm
* (n.d.). Retrieved from https://nccastaff.bournemouth.ac.uk/jmacey/RobTheBloke/www/research/maya/mfntexture.htm
* (n.d.). Retrieved from http://help.autodesk.com/view/MAYAUL/2018/ENU/?guid=__files_DAG_Hierarchy_DAG_walking_example_htm
* (n.d.). Retrieved from http://help.autodesk.com/view/MAYAUL/2018/ENU/?guid=__files_DAG_Hierarchy_DAG_paths_htm
* (n.d.). Retrieved from http://help.autodesk.com/view/MAYAUL/2018/ENU/?guid=__files_GUID_F584322E_4A12_4995_9F4E_A38D4331808F_htm
* Get a mesh node from a transform node in Maya C API. (n.d.). Retrieved from https://stackoverflow.com/a/30402399
* Get a mesh node from a transform node in Maya C API. (n.d.). Retrieved from https://stackoverflow.com/questions/30394017/get-a-mesh-node-from-a-transform-node-in-maya-c-api
* Get texture filename from Maya python script? (n.d.). Retrieved from https://stackoverflow.com/questions/42658729/get-texture-filename-from-maya-python-script
* MFnMesh Class Reference [OpenMaya - API module for common classes, FunctionSet classes]. (n.d.). Retrieved from https://download.autodesk.com/us/maya/2011help/API/class_m_fn_mesh.html
* S., & K. (2010, January 09). Accessing a mesh's material in Maya API. Retrieved from https://forums.cgsociety.org/t/accessing-a-meshs-material-in-maya-api/1393544

