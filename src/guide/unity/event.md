---
title: Event
type: guide_unity
order: 40
---

`EventDispatcher`Is the center of event distribution, GObject is an EventDispatcher. Each event type corresponds to one`EventListener`To receive events and call handlers.

For example, write processing logic for a component click:

```csharp
aObject.onClick.Add(aCallback);
    void aCallback()
    {
        //some logic
    }
```

## Bubbling and catching

Some special events, such as mouse / touch events, have the characteristics of passing to the parent component. This passing process is called bubbling. For example, when a finger touches the A component, the A component triggers the TouchBegin event, then the parent component B of the A component triggers the TouchBegin event, and then the parent component C of the B component also triggers the TouchBegin event, and so on until the root of the stage. This design guarantees that all related display objects have the opportunity to handle touch events, not just the topmost display object.

The bubbling process can be interrupted. By calling EventContext.StopPropagation (), the bubbling can be stopped to advance to the parent component.

As can be seen from the bubbling process above, the order of event processing should be: A's listeners-> B's listeners-> C's listeners. There is also a mechanism that allows any object on the link to process events in advance. This is event capture. . Event capture is reversed. For example, in the above example, C captures the event first, then B, and then A. So the complete sequence of all event processing should be:

C’s capture listeners->B’s capture listeners->A’s capture listeners->A’s listeners->B’s listeners->C’s listeners

Capturing delivery chains cannot be aborted, and bubbling delivery chains can be aborted with StopPropagation.
Event capture is designed to allow parent components to inspect events over child and grandchild components.

Not all events have a bubbling design. In non-bubbling events, capture callbacks are better than ordinary callbacks, nothing more, and can be used as a priority feature.

## Event callback function

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
      Debug.Log(((GObject)context.sender).data)
  }
```

## EventListener

- `Add` `Remove`Add or delete a callback.
- `AddCapture` `RemoveCapture`Add or delete a capture period callback.

## EventContext

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

## InputEvent

For keyboard events and mouse / touch events, you can get the relevant data of such events through EventContext.inputEvent.

- `x` ``y The position of the mouse or finger; this is the stage coordinates, because the UI may be scaled due to self-adaptation, so if you want to convert to the coordinates in the UI element, use GObject.GlobalToLocal transformation.

- `keyCode`Key code

- `modifiers`Reference UnityEngine.EventModifiers.

- `mouseWheelDelta`Mouse wheel scroll value.

- `touchId`Finger ID associated with the current event; on PC platforms, this value is 0, meaningless.

- `isDoubleClick`Whether to double-click.


