---
title: "Bindless Table"
date: 2024-01-08T0:0:0-00:01
categories:
  - Blog
---

## Table Layout

I've been experimenting with Bindless rendering using DirectX12 & Vulkan APIs exclusively, and here is the layout I currently use :

| Type                            | First      | Last  | Bits | Space | Binding
| ------------------------------- | -----------| ------| ---- | ----- | --------
| Texture1D (float4)              | 0          | 16383 | 14   | 10    | 0  
| Texture2D (float4)              | 0          | 16383 | 14   | 20    | 0  
| Texture2D (uint)                | 0          | 16383 | 14   | 21    | 0  
| Texture3D (float4)              | 0          | 16383 | 14   | 30    | 0  
| ByteAddressBuffer               | 16384      | 32767 | 14   | -     | 1  
| RWTexture1D                     | 32768      | 40959 | 13   | 110   | 2  
| RWTexture2D                     | 32768      | 40959 | 13   | 120   | 2  
| RWTexture3D                     | 32768      | 40959 | 13   | 130   | 2  
| RWByteAddressBuffer             | 40960      | 49151 | 13   | 100   | 3  
| RaytracingAccelerationStructure | 49152      | 49167 | 4    | -     | 4  

Notes:
- The DirectX12 version uses the *Space* value to alias the different types of resource view formats 
- The Vulkan version uses the *Binding* value to differenciate between the different types of resource views
- Several TLAS can be used if multiple views are used. The current value implies a maximum of 16 views using RayTracing.

In case you want more details, check the file [table.hlsi](https://github.com/vimontgames/vgframework/blob/master/data/Shaders/system/table.hlsli) in the repo.

## Reserved Slots

By default, a new resource requiring a view will be created allocating a new bindless handle in these ranges, but it's convenient to have some hard-coded slots :

- To avoid an extra indirection (e.g. passing buffer handle as root constant)
- To fallback to missing texture
- To initialize shader structs with proper default values (e.g. default albedo, default normal map etc...)

These hardcoded slots are allocated bottom-up in addition to the last value of the range being used as invalid value.

### Texture

| Slot             | Value  
| ---------------- | ------
| PBR              | 16379 
| Normal           | 16380 
| Albedo           | 16381 
| ImGui            | 16382 
| Default          | 16383 

### Buffer

| Slot             | Value  
| ---------------- | ------
| InstanceData     | 32763 
| SkinningMatrices | 32764 
| LightsConstants  | 32765 
| ViewConstants    | 32766 
| Default          | 32767 

 