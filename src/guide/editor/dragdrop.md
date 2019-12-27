---
title: Drag&Drop
type: guide_editor
order: 32
---

## Free drag and drop

To make a component draggable, it's very simple, just set the draggable property, for example:

```csharp
aObject.draggable = true;
```

After setting, when the player holds the component, they can drag it at will. You can set a rectangle to limit the drag range:

```csharp
// Note that the rectangular range here uses the coordinates on the stage, not the local coordinates of the component.
    aObject.dragBounds = new Rect (100,100,200,200);
```

You can get notifications about the start of dragging, the process of dragging, and the end of dragging:

```csharp
//Unity/Cry
    aObject.onDragStart.Add(onDragStart);
    aObject.onDragMove.Add(onDragMove);
    aObject.onDragEnd.Add(onDragEnd);

    //AS3
    aObject.addEventListener(DragEvent.DRAG_START, onDragStart);
    aObject.addEventListener(DragEvent.DRAG_MOVING, onDragMove);
    aObject.addEventListener(DragEvent.DRAG_END, onDragEnd);

    //Egret
    aObject.addEventListener(fairygui.DragEvent.DRAG_START, this.onDragStart, this);
    aObject.addEventListener(fairygui.DragEvent.DRAG_MOVING, this.onDragMove, this);
    aObject.addEventListener(fairygui.DragEvent.DRAG_END, this.onDragEnd, this);

    //Laya
    aObject.on(fairygui.Events.DRAG_START, this, this.onDragStart);
    aObject.on(fairygui.Events.DRAG_MOVE, this, this.onDragMove);
    aObject.on(fairygui.Events.DRAG_END, this, this.onDragEnd);

    //Cocos2dx
    aObject->addEventListener(UIEventType::DragStart, CC_CALLBACK_1(AClass::onDragStart, this));
    aObject->addEventListener(UIEventType::DragMove, CC_CALLBACK_1(AClass::onDragMove, this));
    aObject->addEventListener(UIEventType::DragEnd, CC_CALLBACK_1(AClass::onDragEnd, this));

    //CocosCreator
    aObject.on(fgui.Event.DRAG_START, this.onDragStart, this);
    aObject.on(fgui.Event.DRAG_MOVING, this.onDragMove, this);
    aObject.on(fgui.Event.DRAG_END, this.onDragEnd, this);
```

## Alternative drag

If you don't want to click anywhere on the component and drag it, you can use alternative drag. For example, the window in FairyGUI, click its title bar, you can drag the window, we use this as an example to analyze how to achieve:

```csharp
// Set the drag area to be draggable, and then listen for the drag start event
    _dragArea.draggable = true;
    _dragArea.onDragStart.Add (onDragStart);

    // Unity / Cry / MonoGame
    void onDragStart (EventContext context)
    {
        // Cancel the source drag, that is, _dragArea will not be dragged actually
        context.PreventDefault ();
    
        // Set the window to be dragged. context.data is the id of the finger.
        this.StartDrag ((int) context.data);
    }

    // Laya
    onDragStart (evt: laya.events.Event) {
        var obj: fairygui.GObject = fairygui.GObject.cast (evt.currentTarget);

        // Cancel the original target and replace it with a substitute
        obj.stopDrag ();

        this.startDrag ();
    }

    // CocosCreator
    onDragStart (evt: fgui.Event) {
        let obj: fgui.GObject = fgui.GObject.cast (evt.currentTarget);
        
        // Cancel the original target and replace it with a substitute
        obj.stopDrag ();

        this.startDrag ();
    }
```

In the above way, when _dragArea is attempted to be dragged, it will be converted to the drag of the Window itself.

## Substitute drag

Dragging can only be moved within the parent component of the component. If you need to move within the full screen, you need to use dragging by substitute. The startup method of avatar drag needs to be converted first:

```csharp
aObject.draggable = true;
    aObject.onDragStart.Add (onDragStart);

    // Unity / Cry / MonoGame
    void onDragStart (EventContext context)
    {
        // Cancel source drag
        context.PreventDefault ();
    
        // icon is the avatar image of this object, userData can be arbitrary data, and the underlying layer does not parse it. context.data is the id of the finger.
        DragDropManager.inst.StartDrag (null, icon, userData, (int) context.data);
    }
```

After using the double drag, if you want to detect the end of the drag, you can no longer listen to the original object, you should use:

```csharp
//Unity/Cry/MonoGame
    DragDropManager.inst.dragAgent.onDragEnd.Add(onDragEnd);

    //CocosCreator
    DragDropManager.inst.dragAgent.on(fgui.Event.DRAG_END, this.onDragEnd, this);
```

DragDropManager also provides the commonly used drag-> drop function. If a component needs to receive events that other components drag into it and release, you can use:

```csharp
aComponent.onDrop.Add (onDrop);

    // Unity / Cry / MonoGame
    void onDrop (EventContext context)
    {
        // here context.data is the userData passed in StartDrag
        Debug.Log (context.data);
    }

    // CocosCreator
    void onDrop (dropTarget: fgui.GObject, userData: any) {
        // here sourceData is the userData passed in by StartDrag
    }
```

DragDropManager uses an image resource to represent the alias. This image is displayed by the loader. This loader is DragDropManager.inst.dragAgent, you can adjust its parameters to fit the actual project needs.

If your stand-in is not as simple as an image, for example, you need to use a component as a stand-in, then you can define your own DragDropManager, copy a DragDropManager directly, modify and write your own logic on it, and then use your DragDropManager. The design of the DragDropManager class does not take into account all practical situations, it is only used as a reference.