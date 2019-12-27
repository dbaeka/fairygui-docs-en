---
title: Input
type: guide_unity
order: 30
---

## Mouse / touch input

FairyGUI uses built-in mechanisms for handling mouse and touch events, without using rays. If you really want to use rays, you can set the UIPanel's "HitTest Mode" to "Raycast". Regardless of the click detection mode, the following event processing mechanism is the same.

If you want to distinguish between clicking on the UI or clicking on objects in the scene, you can use the following methods:

```csharp
if (Stage.isTouchOnUI) // Click on the UI
    {
    }
    else // no point on UI
    {
    }
```

This detection works not only for clicks but also for hovering. For example, this judgment is also true if the mouse is hovering over the UI. **If you think there is no UI on the screen and this still returns true, then there is really a UI**, Especially the full screen interface, when no penetration is set, its blank area also accepts input. You can check by printing GRoot.inst.touchTarget.

The mouse / touch related events are:

- `onTouchBegin`The mouse button is pressed (left, middle, right), or the finger is pressed. Mouse buttons can be obtained from context.inputEvent.button, 0-left button, 1-middle button, 2-right button.
- `onTouchMove`The mouse pointer moves or your finger moves on the screen. There are only two cases of this event:
   - If context.CaptureTouch () is called in onTouchBegin, subsequent movement events will be triggered on this object (regardless of whether the position of the finger or pointer is above the object).
   - The stage's onTouchMove will always fire, Stage.inst.onTouchMove, it does not need to use CaptureTouch to capture. As long as the mouse is moved on the PC platform, it can be triggered; on the mobile platform, it is triggered only when the finger is pressed to move.
- `onTouchEnd`The mouse button is released or your finger is off the screen. If the mouse or touch position is no longer within the scope of the component, then the component's TouchEnd event will not be triggered. If it is really needed, you can call context.CaptureTouch () in onTouchBegin to request capture.
- `onClick`Mouse or finger click. You can determine whether you double-clicked from context.inputEvent.isDoubleClick.**If you are looking for a long press event, then use LongPressGesture[(Long press gesture)](#手势)。**
   **After the finger is pressed, if Stage.inst.CancelClick is called, the Click event will not be triggered when the finger is raised.**
- `onRightClick`Context.
- `onRollOver`Fired when the mouse or finger moves into an element.
- `onRollOut`Fired when the mouse or finger moves out of a component.

You can get the current mouse or finger position and the clicked object in any event (that is, not just mouse / touch related events) callbacks, such as:

```csharp
void AnyEventHandler (EventContext context)
    {
        // Click the position, pay attention to the screen coordinates. To convert the local coordinates, use GlobalToLocal.
        Debug.Log (context.inputEvent.x + "," + context.inputEvent.y);

        // Get the clicked object (in mouse / touch event)
        Debug.Log ((GObject) context.sender);
        // If the event is bubbling, you can get the lowest level object. But note that the object type here is DisplayObject, not GObject.
        Debug.Log ((DisplayObject) context.initiator);
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
    Debug.Log (testComponent == obj || testComponent.IsAncestorOf (obj));
```

## Multi-touch

FairyGUI supports multi-touch processing. Each finger will dispatch events according to the TouchBegin-> TouchMove-> TouchEnd process. You can use EventContext.inputEvent.touchId to distinguish different fingers. In general, ordinary click events do not need to care about the finger id, only those that need to use the entire touch process need to be processed.

```csharp
// This is the number of fingers currently pressed
    int touchCount = Stage.inst.touchCount;

    // Get all currently pressed finger ids
    int [] touchIDs = Stage.inst.GetAllTouch (null);
```

If you don't want to use the multi-touch function, you can use Unity's API: Input.multiTouchEnabled = false to turn it off.

## VR input processing

The input in VR generally uses gaze input or handle input. For these new input methods, FairyGUI provides package support. That is to say, in VR applications, you can still handle VR input like mouse or touch input. No difference, which means that the UI logic does not need to be modified.

First, you need to pass these external inputs into FairyGUI. These APIs are provided in the Stage class:

```csharp
public void SetCustomInput(ref RaycastHit hit, bool buttonDown);
    public void SetCustomInput(ref RaycastHit hit, bool buttonDown, bool buttonUp);
```

- `hit`If there is no handle, here the ray hit by the eye (actually the ray of the camera) hits the target; if there is a handle, the ray hit by the input handle hits the target.

- `buttonDown`Is there a key press? For APIs without a buttonUp parameter, the system will automatically simulate a buttonUp in the next frame.

- `buttonUp`Whether any buttons are released. The whole process of pressing and releasing constitutes one click. If you only press and do not release, there will be no click trigger, you must pay attention to this.

SetCustomInput can be called in Update and must be**Called every frame**。 If SetCustomInput is used, FairyGUI no longer processes mouse or touch input.

Example of use:

```csharp
SteamVR_TrackedObject controller;
SteamVR_Controller.Device controllerDevice;

void Start()
{
    controller = ...
    controllerDevice = ...
}

void Update() 
{
    Vector3 pos = controller.transform.position;
    Vector3 dir = controller.transform.forward;
    bool trigger_down = controllerDevice.GetPress(Valve.VR.EVRButtonId.k_EButton_SteamVR_Trigger);

    RaycastHit rh;
    if (Physics.Raycast(pos, dir, out rh))
    {
        Stage.inst.SetCustomInput(rh, trigger_down);
    }
}
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

On the phone, it is entered through the native keyboard. When the keyboard pops up, the GTextInput.onFocusIn event is dispatched, and when the keyboard is retracted, the GTextInput.onFocusOut event is dispatched.

Unity comes with an extra input box when typing on the keyboard. If you do n’t need this input box and want to pop up your own input box like WeChat, you need to write your own code.

```csharp
// define your own keyboard
    KeyBoard yourKeyboard;

    Stage.inst.keyboard = yourKeyboard;
```

**Copy-paste problem**

When using a plug-in in the form of a DLL, because the DLL is compiled for the mobile platform by default, copy and paste is not supported (if you want to support it, you need to write native code support yourself). If it is used on the PC platform,[CopyPastePatch.cs](https://github.com/fairygui/FairyGUI-unity/blob/master/Examples.Unity5/Assets/FairyGUI/CopyPastePatch.cs)Put it in the project, and call CopyPastePatch.Apply () when the game starts, you can activate the copy and paste function on the PC platform. **If you are using a plugin in source form, you don't need to do this.**

## gesture

FairyGUI provides gesture support. The ways to use gestures are:

```csharp
LongPressGesture gesture = new LongPressGesture(targetObject);
    gesture.onAction.Add(OnGestureAction);
```

The targetObject is the component that receives the gesture. Note that it must be touchable. The image is not touchable, and it is generally recommended to use components or loaders. If you need to monitor gestures in full screen, you can directly use GRoot.inst as the targetObject (requires 1.9.0 SDK or later)

Common gestures are:

`LongPressGesture`Long press gesture. You can set the time for the long press trigger, and you can also control the time interval for the notification to continue to be triggered after the long press trigger.

`SwipeGesture`Finger swipe gesture.

`PinchGesture`Two-finger zoom gesture.

`RotationGesture`Two-finger rotation gesture.

Refer to Gesture Demo for gesture usage. Gestures are decoupled from the FairyGUI SDK. That is, if you do n’t think the gestures meet your requirements, you can copy one and use it yourself.
