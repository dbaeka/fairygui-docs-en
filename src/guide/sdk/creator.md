---
title: Cocos Creator
type: guide_sdk
order: 4
---

## Run Demo

1. Download the `FairyGUI-cocoscreator` example directly from GitHub Clone or from the [Official Product Page](../../product/index.html#CocosCreatorSDK).
2. The SDK is based on Cocos Creator version 2.x. Version 1.x isn't supported, so please use version 2.0 or higher of Creator. It's recommended to use 2.0.7 or above.
3. Open the example Cocos Creator project.
4. Run it to see it in action.
5. The UI interface works in the UIProject directory and needs to be opened for viewing and modification using the FairyGUI editor. This part of the file is independent of Creator and doesn't need to be placed in the `Assets` directory of Creator.
6. The example is written in TS, but the core library is fairygui.js, so there's no problem with using JS development.
7. `fairygui.js` is uncompressed. Cocos will compress it itself for release. The final size will be around 300K.

## Load UI Package

FairyGUI organizes the UI interface in units of packages. A UI package may include one or more interfaces. After each UI package is released, it will get a description file with a `bin` suffix and one or more pictures as the texture set. These files need to be placed in the resources directory for dynamic loading. In order to avoid complicated dependency relationships, and to avoid management difficulties in the future, our interface isn't directly referenced by things in the scene. A UI package can be loaded and unloaded at any time.

Publish the package directly to the `Assets/Resources` directory of Creator or its subdirectories in the FairyGUI editor.

![](../../images/20181214102714.png)

Note that the picture is set to RAW format and doesn't need to be set to Sprite. Because FairyGUI will analyze the Sprite itself.

There are two ways to load a package in your code. One is that you are responsible for loading the file, and the second is for FairyGUI to load it yourself. The first way is to make it easy for you to do a general loading of other resources, or to show the progress (like a loading bar).

First method:

```csharp
    // Here we are using relative resource paths
    let res = [
        "UI/Bag",  //Description file
        "UI/Bag_atlas0" //Texture set
    ];
    cc.loader.loadResArray(res, function(err, assets) {
        // Call addPackage after loading
        fgui.UIPackage.addPackage("UI/Bag");

        // Now you can start creating the interface in the package.
    });
```

Second method:
```csharp
    // Here we are using a relative resource path
    fgui.UIPackage.loadPackage("UI/Bag", function(err) {
        // There is no need to call addPackage here, you can start creating the interface directly.
    });
```
## Uninstall UI Package

When a package is no longer in use, it can be uninstalled.

```csharp
    // Here you can use the package id, the package name, or the package path
    fgui.UIPackage.removePackage("Bag");
```

After the package is uninstalled, all the resources contained in the package will be unloaded, and the components created by the package will not be displayed properly (although no error will be reported), so these components should be (or should have been) destroyed.
It's generally not recommended that the package be loaded and unloaded frequently, because each loading/unloading necessarily consumes CPU time (meaning power consumption) and generates a large amount of GC. The memory occupied by the UI system can be accurately estimated. You can set which packets are in memory according to the frequency of use of the package (recommended as much as possible).

## Create GRoot

Each scene needs to have a `GRoot`, which is the root node of the UI. After the scene is loaded, you need to manually create a `GRoot`.

```csharp
    fgui.GRoot.create();
```

## Create UI

```csharp
    let view:fgui.GComponent = fgui.UIPackage.createObject(“包名”, “组件名”).asCom;

    // The following methods can display the view:

    // 1，Display by directly adding to GRoot
    fgui.GRoot.inst.addChild(view);

    // 2，Display using a Window
    aWindow.contentPane = view;
    aWindow.show();

    // 3，Adding to another component
    aComponnent.addChild(view);
```

If there's too much interface content, it may lag when it's created. FairyGUI provides a way to create a UI asynchronously. In the asynchronous creation mode, the CPU resources consumed per frame will be controlled, but the creation time will be slightly longer than the synchronization creation. E.g:

```csharp
    let handler = new AsyncOperation();

    fgui.UIPackage.createObjectAsync("包名","组件名", myCreateObjectCallback);

    void myCreateObjectCallback(obj:fgui.Gobject)
    {
    }
```

Closing/hiding the interface:

```csharp
    // If the texture set is added to GRoot or other parent nodes...
    view.removeFromParent();

    // If it's a window
    view.hide();
```

If the interface is no longer used, you can destroy it:

```csharp
    view.Dispose();
```

When the scene is switched, all interfaces will be destroyed. If you don't want them to be destroyed, you need to create the interface, set the root node as parent, and ensure that the interface is closed before switching the scene.

```csharp
    cc.game.addPersistNode(view.node);
```

## Coordinate System

The x/y/position values in `GObject` are all **local coordinates**, which is the offset from the parent. `GObject` doesn't provide a direct property to get the global coordinates of the object, but provides a way to do the conversion.

If you want to get the coordinates of any UI component on the screen, you can use:

```csharp
    let screenPos:cc.Vec2 = aObject.localToGlobal(cc.Vec2.ZERO);
```

**(Note that the screen mentioned here refers to the screen in the semantics of FairyGUI, which is based on the upper left corner of the screen, NOT the screen in Creator's semantics)**

Conversely, if you want to get the local coordinates of the screen coordinates on the UI component, you can use:

```csharp
    let localPos:cc.Vec2 = aObject.globalToLocal(screenPos);
```

## Event System

FairyGUI directly uses Creator's event system, so `GObject.on`/`GObject.off` is actually implemented by GObject.node.on/off, that is, any event can be performed through `GObject.node`, including custom events. In the event callback, the currentTarget in `cc.Event` reflects which node the event was dispatched from. If you want to get the `GObject` corresponding to this node, you can use this method:

```
    aObject.on(someEventName, this.onHandle, this);

    onHandle(evt:cc.Event) {
        cc.log(evt.currentTarget); //Node object
        cc.log(fgui.GObject.cast(evt.currentTarget)); //GObject object
    }
```

## Mouse/Touch Event

For mouse events and touch events, FairyGUI uses custom events. The constants are defined in fgui.Event. This isn't the same as Creator's own `cc.Node.EventType.TOUCH_BEGIN`. Pay attention to the difference. Because Creator's own touch logic is difficult to handle penetration/non-penetration, as well as custom area clicks.

The mouse/touch event callback function has one parameter: `evt:fgui.Event`, and `fgui.Event` inherits from `cc.Event`.

- `TOUCH_BEGIN` Fires when a mouse button (left, center, right) is clicked, or a touch is started. The mouse button can be obtained from evt.button, 0-left button, 1-center button, 2-right button. If it's a touch event, you can get the finger ID from `evt.touchId`; if it's a mouse event, `evt.touchId` is always 0.

- `TOUCH_MOVE` Fires when the mouse pointer, or a finger, is moved across the screen. There are only two situations in this event that can be triggered. 1. When `evt.captureTouch()` is called in `TOUCH_BEGIN`, subsequent move events will be fired on this object (regardless of whether the finger or pointer position is above the object). 2. `TOUCH_MOVE` on `GRoot` will always trigger, no need to use `captureTouch` capture.

- `TOUCH_END` Fires when the mouse button is released, or when a finger is removed from the screen. If the mouse or touch location is no longer in the `GObject` scope, the component's `TouchEnd` event will not be triggered. If it's needed, it can be called by calling `evt.captureTouch()` in `TOUCH_BEGIN`.

- `CLICK` Click on the event. It can be determined from `evt.isDoubleClick` whether to click or double click. There is a shortcut to listen for click events: `GObject.onClick(callback,...)`, which is more concise than `GObject.on(fgui.Event.CLICK,...)`.

- `ROLL_OVER` Fires when the mouse pointer or finger moves into the display object area.

- `ROLL_OUT` Fires when the mouse pointer or finger moves out of the display object area.

- `MOUSE_WHEEL` Fires when the mouse is scrolled.

If you're not in the event callback function and need to get the current mouse or finger position, you can use:

```csharp
    // touchId is the finger id. If you don't care about this, don't pass it.
    let pos1:cc.Vec2 = fgui.GRoot.inst.getTouchPosition(touchId);
```

At any time, if you need to get the object you're currently clicking, or the object under the mouse, you can get it in the following way:

```csharp
    let obj:fgui.GObject = fgui.GRoot.inst.touchTarget;

    // Determine if it's inside a component
    cc.Log(testComponent.isAncestorOf(obj));
```

## Font

These steps are required if you want to use TTF fonts:

1、First you need to get the cc.Font object, which is obtained from loadRes, or directly obtained from the script in the scene, according to the project requirements.

2、Use fgui.UIConfig.registerFont to register the cc.Font with the font name used in FairyGUI, assuming that `aFont` is the cc.Font object:

```csharp
    fgui.UIConfig.registerFont('myfont', aFont);
```

3、If it's a global font:

```csharp
    fgui.UIConfig.defaultFont = 'myFont';
```

4、If this is a font specified separately for a text, for example:

![](../../images/2016-07-06_143622.png)

Here we use a font named "Black Body", which is a different font from UIConfig.defaultFont. We need to register this font, which looks like:

```csharp
    fgui.UIConfig.registerFont('Black Body', aFont);
```