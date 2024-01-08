---
title: "Bindless Table"
date: 2024-01-08T0:0:0-00:01
categories:
  - Rendering
tags:
  - Bindless
---

## Table Layout

I've been experimenting with Bindless rendering using DirectX12 & Vulkan APIs exclusively, and here is the layout I currently use :

| Type                            | Range          | Space | Binding
| ------------------------------- | -------------- | ----- | --------
| Texture1D (float4)              | [0    ..16383] | 10    | 0  
| Texture2D (float4)              | [0    ..16383] | 20    | 0  
| Texture2D (uint)                | [0    ..16383] | 21    | 0  
| Texture3D (float4)              | [0    ..16383] | 30    | 0  
| ByteAddressBuffer               | [16384..32767] | -     | 1  
| RWTexture1D                     | [32768..40959] | 110   | 2  
| RWTexture2D                     | [32768..40959] | 120   | 2  
| RWTexture3D                     | [32768..40959] | 130   | 2  
| RWByteAddressBuffer             | [40960..49151] | 100   | 3  
| RaytracingAccelerationStructure | [49152..49167] | -     | 4  

Notes:
- The DirectX12 version uses the *Space* value to alias the different types of resource view formats 
- The Vulkan version uses the *Binding* value to differenciate between the different types of resource views
- Several TLAS can be used if multiple views are used. The current value implies a maximum of 16 views using RayTracing.

In case you want more details, check the file [table.hlsi](https://github.com/vimontgames/vgframework/blob/master/data/Shaders/system/table.hlsli) in the repo.

## Reserved Slots

By default, a new resource requiring a view will be created allocating a new bindless handle in these ranges, but it's convenient to have some hard-coded slots :

- To avoid an extra indirection (e.g. passing buffer handle as root constant) :
```hlsl
PS_Output PS_Forward(VS_Output _input)
{
    PS_Output output = (PS_Output)0;
    
    ViewConstants viewConstants;
    viewConstants.Load(getBuffer(RESERVEDSLOT_BUFSRV_VIEWCONSTANTS));

    [...]
```

- To fallback to missing texture :
```c++
// create default texture at slot 'invalidBindlessTextureHandle'
m_defaultTexture = device->createTexture(texDesc, "DefaultTex2D", initData, ReservedSlot(BINDLESS_TEXTURE_INVALID));
VG_ASSERT(m_defaultTexture->getTextureHandle() == BINDLESS_TEXTURE_INVALID);
        
// copy texture to all 'texture' slots
for (uint i = BINDLESS_TEXTURE_START; i < BINDLESS_TEXTURE_COUNT; ++i)
    if (BINDLESS_TEXTURE_INVALID != i)
        copyTextureHandle(i, m_defaultTexture);
```

- To initialize shader structs with proper default values (e.g. default albedo, default normal map etc...) :

```hlsl
struct GPUMaterialData
{
    #ifdef __cplusplus
    GPUMaterialData()
    {
        setAlbedoTextureHandle(RESERVEDSLOT_TEXSRV_DEFAULT_ALBEDO);
        setNormalTextureHandle(RESERVEDSLOT_TEXSRV_DEFAULT_NORMAL);
        setPBRTextureHandle(RESERVEDSLOT_TEXSRV_DEFAULT_PBR);
    }   
    #endif 

    // .x = albedo | normal 
    // .y = pbr | unused 
    uint4 textures; 

    [...]
};
```

These custom hardcoded slots are defined compile-time and "allocated" bottom-up before the last value of the range used as invalid value.

| Type        | Name             | Value  
| -------     | ---------------- | ------
| Texture     | PBR              | 16379 
| Texture     | Normal           | 16380 
| Texture     | Albedo           | 16381 
| Texture     | ImGui            | 16382 
| **Texture** | **Default**      | **16383** 
| Buffer      | InstanceData     | 32763 
| Buffer      | SkinningMatrices | 32764 
| Buffer      | LightsConstants  | 32765 
| Buffer      | ViewConstants    | 32766 
| **Buffer**  | **Default**      | **32767**


 