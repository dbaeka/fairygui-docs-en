---
title: Insert 3D
type: guide_unity
order: 50
---

FairyGUI provides a very complete solution to solve the problem of 3D objects interspersed with UI such as models, particles, and skeleton animation, and supports interspersion with UGUI Canvas.

## Put directly into 3D objects

This method is to directly render the 3D object with the UI camera. Compared with the RenderTexture solution, it is simple to use and saves memory. The disadvantage is that the 3D object has no perspective under the UI camera.

Inserting 3D objects in the UI requires a graphic placeholder and`GoWrapper`Object.

1. Instantiate your 3D object, for example:

```csharp
Object prefab = Resources.Load("Role/npc");
  GameObject go = (GameObject)Object.Instantiate(prefab);
```
2. Set the appropriate position, scale, and rotation for the 3D object.

```csharp
go.transform.localPosition = new Vector3(61, -89, 1000); 
  go.transform.localScale = new Vector3(180, 180, 180);
  go.transform.localEulerAngles = new Vector3(0, 100, 0);
```

Note: For objects such as models with “thickness” (there is a certain range on the z axis), the z value of localPosition should not be 0. You can set a larger positive value (a positive number means away from the camera). Because Shader has ZTest turned on, if the model's z-axis coordinate is 0 and the z value of the UI is the same, then his front end may overlap with the UI of the previous layer.

The scale of the model can be estimated according to this formula: zoom factor = display size (unit pixels) / model size (units meters). For example, if a model is 1 meter high and eventually needs to display 400 pixels high, then you need to zoom in 400 times.

2. Place a blank graphic in the UI, assuming the name is "holder".

```csharp
GGraph holder = view.GetChild("holder").asGraph;
```

3. Construct a GoWrapper object and place it in the holder.

```csharp
GoWrapper wrapper = new GoWrapper(go);
  holder.SetNativeObject(wrapper);
```

**Click processing**

GoWrapper has no size by default, so it cannot handle click events. If you need a click event on the display area of the 3D object, you can place another graphic (with transparency set to 0) on the holder as the click area, or an empty component will do.

**Debugging method**

To insert a 3D object in this way, you need to use code, and you can see the effect at runtime. It is not very intuitive to set the position, rotation, and scaling of the GameObject. FairyGUI provides an intuitive way to hang 3D objects directly under UIPanel objects. **First set the Layer of the 3D object to UI**, And check "Set Native Children Order" in UIPanel. as follows:

![](../../images/20170809140223.png)

But this method GameObject does not move with UIPanel, so it can only be used for debugging purposes. For example, when making particles for UI, this method can be provided to art.

**Update GameObject**

GoWrapper will query all the Renderers in your GameObject in the constructor and save them. If your GameObject changes in the future, you need to tell GoWrapper to re-query and save, otherwise the display will be incorrect.

```csharp
wrapper.CacheRenderers ();
```

**Replace GameObject**

If you want to modify the object wrapped by GoWrapper, you can use:

```csharp
wrapper.wrapTarget = anotherGameObject;
```

After setting a new wrapper object, the original wrapper object will only be deleted by reference, but will not be destroyed. If you want to destroy the original GameObject, you must handle it yourself, for example:

```csharp
Object.Destroy(wrapper.wrapTarget);
    wrapper.wrapTarget = anotherGameObject;
```

**Copy material**

If the GoWrapper wrapper is used in many places at the same time, and you don't handle the copying of the material when you instantiate it, then there will be some problems. For example, if a model is displayed on the UI and used in the scene, the UI system needs to modify the material parameters of the model. The root cause of this problem is that GoWrapper uses shared materials by default, which is due to efficiency impact. There are two ways to solve this problem. First, copy the material yourself when you instantiate the object. You need to be careful to avoid excessive copy operations. Second, let GoWrapper copy automatically. This can be called when setting the wrapper object:

```csharp
// The second parameter is true, which means that the material is copied
    wrapper.setWrapTarget (anotherGameObject, true);
```

**Tailoring**

If you need to trim a 3D object, you can use a custom mask (**Models, particles, skeletal animation, etc.**）。 Reference of using custom mask:[Component mask](../editor/component.html#遮罩)。

For example, you need to display the model in the items in a list, and you want the model to be properly hidden when the list is scrolled. In this case, the overflow scroll function of the list itself cannot be used. First convert the list to a component, drag a rectangular graphic inside the component to cover the viewport of the list, and then set this graphic as a custom mask for the component.

The model's shader must also be modified accordingly. Add the following code to the Properties section of the model's shader (see FairyGUI-Image.shader):

```csharp
_StencilComp ("Stencil Comparison", Float) = 8
    _Stencil ("Stencil ID", Float) = 0
    _StencilOp ("Stencil Operation", Float) = 0
    _StencilWriteMask ("Stencil Write Mask", Float) = 255
    _StencilReadMask ("Stencil Read Mask", Float) = 255
```

Add the following code to the SubShader section of the model's shader (see FairyGUI-Image.shader):

```csharp
Stencil
    {
        Ref [_Stencil]
        Comp [_StencilComp]
        Pass [_StencilOp] 
        ReadMask [_StencilReadMask]
        WriteMask [_StencilWriteMask]
    }
```

Finally, set GoWrapper to support custom masks:

```csharp
wrapper.supportStencil = true;
```

**Note that if there are multiple objects that are being clipped, their materials cannot share one, otherwise they will display abnormally. The solution is to copy the material. Please refer to the previous paragraph to copy the material.**

## Use RenderTexture

Another way to present 3D content on the UI is to use RenderTexture. The steps for using RenderTexture are more complicated. You need to create a new camera to render the target object, and then direct the camera's output to a RenderTexture. With RenderTexture, we can assign it to Image.texture. Detailed code can refer to[RenderImage](https://github.com/fairygui/FairyGUI-unity/blob/master/Assets/Examples/RenderTexture/RenderImage.cs)。

RenderTexture can set the background color to transparent, which is convenient for blending with the UI. In the example, comment out "this._image.blendMode = BlendMode.Off;". However, if the rendered content contains a transparent map, the display error of the transparent part will occur when mixing with the UI. There are two solutions. The first solution can refer to[Information here](https://blog.uwa4d.com/archives/Severe_MOBA.html), Modify the model or particle shader, and the RenderTexture shader. The second solution is a unique solution provided by FairyGUI, which is to map the background image of the RenderTexture location to the background of the RenderTexture rendering camera, as RenderImage demonstrates:

```csharp
public void SetBackground(GObject image);
    public void SetBackground(GObject image1, GObject image2);
```

As you can see, you can set up to two images. If there are more than two images superimposed behind RenderTexture, it cannot be processed. These two images do not need to be in the same container as RenderTexture, they can be at any level of the UI.

## Insert Canvas

FairyGUI can meet all the requirements of most UI design, both in terms of function and efficiency. Therefore, in projects using FairyGUI, it is rarely necessary to use UGUI again. Most of UGUI's functions need to be completed with plug-ins. FairyGUI is already built in, and many can be completed in the editor with zero scripts. If you really need to use some UGUI plug-ins, and it is not easy to port, FairyGUI also provides a solution to insert UGUI's Canvas into FairyGUI's display level. Proceed as follows:

1. Set Render Mode of Canvas to WorldSpace and Event Camera to Stage Camera.
2. Remove the Canvas Scaler component (if any).
3. Wrap Canvas with GoWrapper:

```csharp
GameObject canvasObject;
  GoWrapper gw = new GoWrapper(canvasObject);
```
