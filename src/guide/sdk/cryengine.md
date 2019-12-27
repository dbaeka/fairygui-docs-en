---
title: Cry Engine
type: guide_sdk
order: 5
---

## Load UI package

Publish the packaged file directly to the Assets directory or its subdirectory of the Cry project.

```csharp
// demo is the file name filled in when publishing
    UIPackage.AddPackage ("demo");
    
    // if in a subdirectory
    UIPackage.AddPackage ("path/demo");
```
## Uninstall UI pack

When a package is no longer used, it can be uninstalled.

```csharp
// Here you can use the package id, package name, and package path.
    UIPackage.RemovePackage ("package");
```

After the package is uninstalled, all the resources such as textures contained in the package will be uninstalled, and the components created in the package will not display properly (although no error will be reported), so these components should (or have been) destroyed.
Frequent loading and unloading of packages is generally not recommended, because each loading and unloading must consume CPU time (meaning power consumption) and generate a lot of GC. The memory occupied by the UI system can be accurately estimated. You can set which packages are resident in memory according to the frequency of use of the packages (as much as possible is recommended).

## Create UI

```csharp
GComponent view = UIPackage.CreateObject ("package name", "component name").asCom;
    
    // The following methods can display the view:
    
    // 1, directly added to GRoot and displayed
    GRoot.inst.AddChild (view);
    
    // 2, use window display
    aWindow.contentPane = view;
    aWindow.Show ();
    
    // 3, add to other components
    aComponnent.AddChild (view);
```

If the content of the interface is too much, it may cause a freeze when creating. FairyGUI provides a way to create the UI asynchronously. In the asynchronous creation mode, the CPU resources consumed by each frame will be controlled, but the creation time will be slightly longer than the synchronous creation. E.g:

```csharp
UIPackage.CreateObjectAsync ("package name", "component name", MyCreateObjectCallback);

    void MyCreateObjectCallback (GObject obj)
    {
    }
```

The dynamically created interface will not be destroyed automatically, such as a backpack window, you do not need to destroy it every time you go through the scene. If you want to destroy the interface, you need to manually call the Dispose method, for example

```csharp
view.Dispose ();
```

## coordinate system

The x / y / position values in GObject are all**Local coordinates**, Which is the offset from the parent component. GObject does not provide direct properties to obtain the global coordinates of the object, but provides methods to convert.

If you want to get the coordinates of any UI element on the screen, you can use:

```csharp
Vector2 screenPos = aObject.LocalToGlobal(Vector2.zero);
```

Conversely, if you want to get the local coordinates of the screen coordinates on the UI element, you can use:

```csharp
Vector2 localPos = aObject.GlobalToLocal(screenPos);
```

If there is global scaling caused by UI adaptation, then the logical screen size is not the same as the physical screen size, and the coordinates of the logical screen are the coordinates in GRoot. If you want to convert local coordinates to logical screen coordinates, you can use:

```csharp
// Physical screen coordinates are converted to logical screen coordinates
    Vector2 logicScreenPos = GRoot.inst.GlobalToLocal (screenPos);
    
    // Transition between UI element coordinates and logical screen coordinates
    aObject.LocalToRoot (pos);
    aObject.RootToLocal (pos);
```

**Note that what we define in the editor and what is handled in the code generally refers to this logical screen coordinate.**

If you want to transform the coordinates between any two UI objects, for example, you need to know the position of coordinate (10,10) in A in B, you can use:

```csharp
Vector2 posInB = aObject.TransformPoint(bObject, new Vector2(10,10));
```

## Event system

`EventDispatcher`Is the center of event distribution, GObject is an EventDispatcher. Each event type corresponds to one`EventListener`To receive events and call handlers.

For example, write processing logic for a component click:

```csharp
aObject.onClick.Add(aCallback);
    void aCallback()
    {
        //some logic
    }
```

### Bubbling and catching

Some special events, such as mouse / touch events, have the characteristics of passing to the parent component. This passing process is called bubbling. For example, when a finger touches the A component, the A component triggers the TouchBegin event, then the parent component B of the A component triggers the TouchBegin event, and then the parent component C of the B component also triggers the TouchBegin event, and so on until the root of the stage. This design guarantees that all related display objects have the opportunity to handle touch events, not just the topmost display object.

The bubbling process can be interrupted. By calling EventContext.StopPropagation (), the bubbling can be stopped to advance to the parent component.

As can be seen from the bubbling process above, the order of event processing should be: A's listeners-> B's listeners-> C's listeners. There is also a mechanism that allows any object on the link to process events in advance. This is event capture. . Event capture is reversed. For example, in the above example, C captures the event first, then B, and then A. So the complete sequence of all event processing should be:

C’s capture listeners->B’s capture listeners->A’s capture listeners->A’s listeners->B’s listeners->C’s listeners

Capturing delivery chains cannot be aborted, and bubbling delivery chains can be aborted with StopPropagation.
Event capture is designed to allow parent components to inspect events over child and grandchild components.

Not all events have a bubbling design. In non-bubbling events, capture callbacks are better than ordinary callbacks, nothing more, and can be used as a priority feature.

### Event callback function

Each event can register one or more callback functions. The function prototype is:

```csharp
public delegate void EventCallback0();
    public delegate void EventCallback1(EventContext context);
```

The two forms of use are the same, the difference is that there is no parameter or a parameter, just for convenience to write less when you do not need to use EventContext.

There is no way to directly pass custom parameters into the callback function. But it can be achieved indirectly in three ways:

1. Through global or module variables;
2. Use lamba expressions, for example:

```csharp
a.onClick.Add(()=>{ ... });
```

3. Put the variable in the data property of the display object. E.g:

```csharp
a.data = ...;

  void aCallback(EventContext context)
  {
      Debug.Log(context.sender.data)
  }
```

### EventListener

- `Add` `Remove`Add or delete a callback.
- `AddCapture` `RemoveCapture`Add or delete a capture period callback.

### EventContext

EventContext is the parameter type of the callback function.

- `sends`Get the distributor of the event.

- `initiator`Get the originator of the event. Generally, the distributor and originator of an event are the same, but if the event has bubbling, it may not be the same. Refer to the bubble description below.

- `type`Event type.

- `inputEvent`If the event is a keyboard / touch / mouse event, you can get the relevant data of this type of event by accessing the inputEvent object.

- `data`The data of the event. Depending on the event, it can have different meanings.

- `StopPropagation`Click the area of the child node, the parent node can also receive touch events, which is the characteristic of event bubbling. If you do not want to pass to the parent node, you can call this method.

- `CaptureTouch`When the left mouse button is released or the finger is raised, if the mouse or touch position is no longer within the component's range, the component's TouchEnd event will not be triggered. If you really need it, you can request a capture. Inside the TouchBegin event handler, you can call context.CaptureTouch (), so that no matter where the mouse is released (even if it is not in the object area), the object's onTouchEnd will be called. Note that it only takes effect once.
After 1.9.1 SDK, if CaptureTouch is called, the GObject.onTouchMove event will be triggered before the finger (or the left mouse button) is raised (regardless of the position of the finger or pointer above the object), until the left mouse button is released or Lift your finger.

- `UncaptureTouch`Cancels the capture of touch events initiated by CaptureTouch.

### InputEvent

For keyboard events and mouse / touch events, you can get the relevant data of such events through EventContext.inputEvent.

- `x` ``y The position of the mouse or finger; this is the stage coordinates, because the UI may be scaled due to self-adaptation, so if you want to convert to the coordinates in the UI element, use GObject.GlobalToLocal transformation.

- `keyCode`Key code.

- `modifiers`See InputModifierFlags.

- `mouseWheelDelta`Mouse wheel scroll value.

- `touchId`Finger ID associated with the current event; on PC platforms, this value is 0, meaningless.

- `isDoubleClick`Whether to double-click.


## Mouse / touch input

If you want to distinguish between clicking on the UI or clicking on objects in the scene, you can use the following methods:

```csharp
if (Stage.isTouchOnUI) // Click on the UI
    {
    }
    else // no point on UI
    {
    }
```

This detection works not only for clicks but also for hovering. For example, this judgment is also true if the mouse is hovering over the UI.

The mouse / touch related events are:

- `onTouchBegin`The mouse button is pressed (left, middle, right), or the finger is pressed. Mouse buttons can be obtained from context.inputEvent.button, 0-left button, 1-middle button, 2-right button.
- `onTouchMove`The mouse pointer moves or your finger moves on the screen. This event will only be triggered in two cases. 1. When context.CaptureTouch () is called in onTouchBegin, subsequent movement events will be triggered on this object (whether the position of the finger or pointer is above the object). 2, the stage's onTouchMove will always trigger, Stage.inst.onTouchMove, do not need to use CaptureTouch to capture.
- `onTouchEnd`The mouse button is released or your finger is off the screen. If the mouse or touch position is no longer within the scope of the component, then the component's TouchEnd event will not be triggered. If it is really needed, you can call context.CaptureTouch () in onTouchBegin to request capture.
- `onClick`Mouse or finger click. You can determine whether you double-clicked from context.inputEvent.isDoubleClick.
- `onRightClick`Context.

You can get the current mouse or finger position and the clicked object in any event (that is, not just mouse / touch related events) callbacks, such as:

```csharp
void AnyEventHandler(EventContext context)
    {
        Debug.Log(context.inputEvent.x + ", " + context.inputEvent.y);

        Debug.Log(context.sender);
        Debug.Log(context.initiator);
    }
```

If you are not in the event and need to get the current mouse or finger position, you can use:

```csharp
// This is the position of the mouse, or the position of the last finger
    Vector2 pos1 = Stage.inst.touchPosition;

    // Get the specified finger position, the parameter is the finger id
    Vector2 pos2 = Stage.inst.GetTouchPosition (1);
```

At any time, if you need to get the current clicked object, or the object under the mouse, you can get it in the following ways:

```csharp
GObject obj = GRoot.inst.touchTarget;

    // determine if it is in a certain component
    Debug.Log (testComponent.IsAncestorOf (obj));
```

## keyboard input

To listen to keyboard input:

```csharp
Stage.inst.onKeyDown.Add(OnKeyDown);

    void OnKeyDown(EventContext context)
    {
        Debug.Log(context.inputEvent.keyCode);
    }
```
