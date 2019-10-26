---
title: Adaptation
type: guide_editor
order: 220
---

FairyGUI provides a UI adaptation strategy that automatically adapts to the resolution of various devices for mobile game development, which means that developers only need to make a set of UIs to adapt to all device resolutions.

## Design Resolution and Device Resolution

Usually we choose a fixed resolution for UI design and production. This resolution is called "design resolution". For example, 1136×640 and 1280×720 are relatively common design resolutions. When a design resolution is selected, the size of the largest UI interface (usually the fullscreen interface) is limited to this resolution.
The resolution of the device at runtime is called the "device resolution". This resolution isn't necessarily the same as the design resolution. You need to display the UI interface to the screen with a certain scale.

## Global Scale

*（Egret/Laya/Cocos2dx does not use the global scale provided by FairyGUI，You need to use their own global scaling strategy, please ignore this section. Reference material:[Egret](http://developer.egret.com/cn/2d/screenAdaptation/explanation) [LayaAir](https://ldc.layabox.com/doc/?nav=zh-as-1-8-0)*

When the design resolution and device resolution are inconsistent, the first thing to do is global scaling. This global scale is transparent to the inside of the UI, which means that all UI interfaces don't need to worry about the existence of this scale. For example, if the overall size is doubled and the size of a window is 400×400 pixels, the window size that will eventually be displayed to the screen will be 800×800 pixels, but if you read the width and height of this window, it will still return 400×400. All internal coordinates won't change.

The way to set global scale is:

```csharp
    GRoot.inst.SetContentScaleFactor(1136，640，ScreenMatchMode.MatchWidthOrHeight);
```

Here 1136 and 640 are the width and height of the design resolution. `ScreenMatchMode` defines the adaptation mode. The available constants are:

- `MatchWidthOrHeight` Scale up and down with a smaller ratio. For example, if the design resolution is 960x640 and the device resolution is 1280×720, then the ratio of the width can be 1280/960=1.33, the ratio of the height is 720/640=1.125, and finally the smaller 1.125 is used as the global zoom. coefficient. This way of scaling ensures that the content is always on the screen after it has been scaled, and it may leave edges, but it won't go beyond the screen.

- `MatchWidth` Scale using a fixed width. The height may exceed the screen.

- `MatchHeight` Scale using a fixed height. The width may exceed the screen.

In the Unity version, in addition to using the API to set global scaling, a Unity component is also provided: `UIContentScaler`. Just hang the `UIContentScaler` component on any `GameObject` in the startup scene. You don't need to hang every scene. Refer to [here](#../unity/index.html#UIContentScaler).

## Logical Screen Size

The screen size after global scaling is the "logical screen size". In the above example, the device resolution is 1280×720, and the global scaling factor is 1.125, then the logical screen size is (1280/1.125=1138, 720/1.125=640)= 1138x640 . The size of the `GRoot` UI root component always fills the "logical screen". The size of the logical screen can be obtained by `GRoot.inst.width` and `GRoot.inst.height`.

## Fullscreen Interface Adaptation

After global scaling, most UIs don't need to be adjusted. With one exception, they are designed to be full-screen. In the above example, at the design resolution, the size of the full-screen interface is 960x640, and we also design full-screen components by this size. After global scaling, the size of the logical screen becomes 1138x640, and the size is inconsistent. At this point we need to resize the component to make it full.

If you're using `UIPanel`, then setting the `Fit Screen` to `Fit Size` on the Inspector is fine; if it's a dynamically created UI, you can use the API:

```csharp
    // Set the component to fullscreen, that is, the size and logical screen size are the same.
    // The inside of the component should be handled in association with the size change.
    aComponent.SetSize(GRoot.inst.width, GRoot.inst.height);
    // Or, a more concise way
    aComponent.MakeFullScreen();
```

一In general, the screen size won't change after the mobile game starts. But if you're developing a desktop game, or a game that supports horizontal and vertical screen switching (the screen size will change), then the fullscreen interface needs to add a constraint on the screen size, ie.:

```csharp
   aComponent.AddRelation(GRoot.inst, RelationType.Size);
```

Of course, this is just one way to handle a fullscreen interface. In some cases, for example, if you select the `MatchHeight` mode, which is a high-priority adaptation method, this method ensures that the vertical direction of the UI interface is always full, and the horizontal direction may exceed the screen. This kind of adaptation requires the designer to have a "safe area" design and to think in those terms (like not being able to arrange content beyond the screen). For example, center the fullscreen interface and sacrifice the content on both sides:

```csharp
    aComponent.x = (GRoot.inst.width – aComponent.width)/2;
```

In this way, the left and right edges will be cropped off by the edge of the screen, which requires the designer to take this into account when designing.

## Automatically Adjust UI Layout

The full-screen component needs to be reset to fullscreen during the adaptation process, and the component size will change. In this case, the associated system needs to be used to automatically lay out the elements in the component in the correct position. Examples can be referred to at [association system](relation.html#instanceresolution).