---
title: Adaptation
type: guide_editor
order: 35
---

FairyGUI provides a UI adaptation strategy for mobile game development that automatically adapts to various device resolutions, which means that developers only need to make a set of UI to adapt to devices of all resolutions.

## Design resolution and device resolution

Usually we choose a fixed resolution for UI design and production, this resolution is called design resolution. For example, 1136 × 640 and 1280 × 720 are more common design resolutions. After selecting a design resolution, the size of the largest UI interface (usually a full-screen interface) is limited to this resolution.
The resolution of the device at runtime is called the device resolution. This resolution is not necessarily the same as the design resolution. The UI interface needs to be scaled and projected onto the screen.

## Global zoom

When the design resolution and device resolution are inconsistent, the first thing to do is global scaling. This global zoom is transparent to the UI internals, that is, all UI interfaces need not bother about the existence of this zoom. For example, if the overall zoom is doubled, and the size of a window is 400 × 400 pixels, then the size of the window displayed on the screen will be 800 × 800 pixels, but if you read the width and height of this window, it will still return 400 × 400 , All internal coordinates will not change.

Egret / Laya / Cocos2dx / CocosCreator does not use the global zoom provided by FairyGUI. You need to use their own global zoom strategy.
- [Egret](http://developer.egret.com/cn/2d/screenAdaptation/explanation)
- [LayaAir](https://ldc.layabox.com/doc/?nav=zh-as-1-8-0)
- [CocosCreator](https://docs.cocos.com/creator/manual/zh/ui/multi-resolution.html)

The way other platforms set global scaling is:

```csharp
GRoot.inst.SetContentScaleFactor (1136，640 ， ScreenMatchMode.MatchWidthOrHeight);
```

Here 1136 and 640 are the width and height of the design resolution. ScreenMatchMode defines the adaptation mode. The available constants are:

- `MatchWidthOrHeight`Take the smaller ratio of width and height to zoom. For example, if the design resolution is 960x640 and the device resolution is 1280 × 720, then the ratio of the wide side is 1280/960 = 1.33, the ratio of the high side is 720/640 = 1.125, and the smaller 1.125 is taken as the global scale coefficient. This zooming method ensures that the content is always inside the screen after zooming, and may be marginal, but not visible beyond the screen.

- `Match Width`Scales at a fixed width. The high side may extend beyond the screen.

- `MatchHeight`Fixed taking a high proportion to scale. The wide edge may extend beyond the screen.

In the Unity version, in addition to using the API to set global scaling, a Unity component is also provided: UIContentScaler. Just hook up the UIContentScaler component to any GameObject in the startup scene. It is not necessary to hang every scene. [Reference here](#../unity/index.html#UIContentScaler)。

## Logical screen

The screen size after global scaling is the logical screen size. In the above example, the device resolution is 1280 × 720 and the global zoom factor is 1.125. Then the logical screen size is (1280 / 1.125 = 1138, 720 / 1.125 = 640) = 1138x640 . The size of the root component of the GRoot UI always fills the logical screen, that is, the size of the logical screen can be obtained through GRoot.inst.width and GRoot.inst.height.

## Full screen interface adaptation

After global scaling, most UIs do not need to make any adjustments, with one exception, which is designed as a full-screen interface. In the above example, at the design resolution, the size of the full-screen interface is 960x640, and we also designed the full-screen component at this size. After the global zoom, the size of the logical screen becomes 1138x640, and the sizes are inconsistent. At this time we need to resize the component to make it full.

If you are using UIPanel, then set the Fit Screen to Fit Size on the Inspector; if it is a dynamically created UI, you can use the API:

```csharp
// Set the component to full screen, that is, the size is the same as the logical screen size.
    // The internal part of the component should be handled well to cope with the size change.
    aComponent.SetSize (GRoot.inst.width, GRoot.inst.height);
    
    // or more concise way
    aComponnet.MakeFullScreen ();
```

Generally speaking, the screen size of mobile games will not change after entering the game. But if you are developing a desktop game, or a game that supports horizontal and vertical screen switching, that is, the screen size will change, then the full screen interface needs to add a constraint on the screen size, that is

```csharp
aComponent.AddRelation(GRoot.inst, RelationType.Size);
```

Of course, this is just one way to handle a full screen interface. In some cases, for example, if you select "MatchHeight" mode, which is a high-priority adaptation method, this method ensures that the vertical content of the UI interface is always full, and the horizontal direction may exceed the screen. This adaptation method requires designers to have a "safe area" design thinking, and cannot arrange content beyond the screen. For example, center the full-screen interface, sacrificing both sides of the content:

```csharp
aComponent.x = (GRoot.inst.width – aComponent.width)/2;
```

In this way, the left and right edges will be cropped by the screen edge, which requires designers to take this into account when designing.

## Automatically adjust UI layout

During the adaptation process of a full-screen component, the full-screen needs to be reset, and then the component size will change. At this time, you need to use the relation system to automatically arrange the elements in the component in the correct position. Examples can refer to[Correlation system](relation.html#Examples)。