---
title: "BLAS Build and Update"
date: 2024-01-08T0:0:0-00:02
categories:
  - Rendering
tags:
  - RayTracing
  - BLAS
  - Skinning
---

Strategy for building BLAS variants and updating BLASes.

# What's the deal with BLASes?

A BLAS is the local accelleration structures required for raytracing stuff using modern graphic APIs. 

Basically you will need BLASes that represent mesh instances in scene, and add them all to a TLAS.

It's easy to build a BLAS right after mesh loading, but still there a few things to consider :

- BLAS need to separate opaque vs. non-opaques batchs
- Dynamic geometry needs to update BLAS when it changes

We end up having a different strategy for static and opaque geometries :


# Static Geometry

These geometries do not change often. Ideally we would like to build them once, share them as much as possible
and destroy them when they are no more needed (no more instance using it).

These geometries are build with the FAST_TRACE flag only because we will never update them.

When building BLAS right after loading a mesh, it would be convenient to know in advance which parts 
are using **alphablend** or **alphatest**, but it's also convenient to be able to use the same mesh 
with different material instances.

In [vgframework](https://github.com/vimontgames/vgframework), the mesh models are cooked without 
knowledge of the materials used and it's up to every **MeshModelInstance** to choose the materials to use.

A **MeshModel** holds a map of its different BLAses variants. A key is built from the materials used by the 
graphic instance so that the same configuration (e.g. Opaque/Opaque/Alphablend) return the same BLAS and 
increase its RefCount or create it when needed.

### Example

#### 3 cubes using the same opaque material :

[![3 cubes using the same opaque material](http://vimontgames.github.io/assets/images/BLAStest/1.png)](http://vimontgames.github.io/assets/images/BLAStest/1.png)
The 3 cube are sharing the same BLAS

#### 3 cubes using different opaque materials :

[![3 cubes using different opaque materials](http://vimontgames.github.io/assets/images/BLAStest/2.png)](http://vimontgames.github.io/assets/images/BLAStest/2.png)
The 3 cube are still sharing the same BLAS, because new materials are still opaque

#### 3 cubes using different materials but one is alphablend :

[![3 cubes using different materials but one is alphablend](http://vimontgames.github.io/assets/images/BLAStest/3.png)](http://vimontgames.github.io/assets/images/BLAStest/3.png)
The 2 opaque cubes are sharing the same BLAS made of one opaque batch while the last one is using itw own 
BLAS made of 1 non-opaque batch


# Dynamic geometry

As opposed to static geometries, dynamic geometries change every frame (e.g. skinned meshes) so we build them
with the FAST_BUILD and ALLOW_UPDATE flags to reduce the cost of this update.

## Skinned geometry

During views culling, the skins instances visible in any view (incl. shadow casting lights POV) are added to a
lock-free list using atomics.

The skinning for all these instances is done once (per LOD) in a single vertex buffer. When updating BLAS their
offset in this buffer is used as with a regular Vertex Buffer excepted here the buffer is the output of skinning
compute shader.

Dynamic geometries BLASes are not shared with this design, but it could be useful in the future if animation
instancing is needed (e.g. to render crowds).





