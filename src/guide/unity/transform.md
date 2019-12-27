---
title: Transform
type: guide_unity
order: 5
---

## Coordinate origin

FairyGUI uses the upper left corner of the screen as the origin, and Unity's screen coordinates use the lower left corner as the origin. Generally, this conversion does not require developer intervention. If you really need to convert these two, you can use:

```csharp
// Unity's screen coordinate system, with the bottom left corner as the origin
    Vector2 pos = Input.mousePosition;

    // Convert to screen coordinates of FairyGUI
    pos.y = Screen.height-pos.y;
```

## Coordinate transformation

The x / y / position values in GObject are all**Local coordinates**, Which is the offset from the parent component. GObject does not provide direct properties to obtain the global coordinates of the object, but provides methods to convert.

**Please note: For an object, the origin of the local coordinates is (0,0), not (x, y), (x, y) is the coordinates of the parent component, not its own local coordinates.**

If you want to get the coordinates of any UI element on the screen, you can use:

```csharp
Vector2 screenPos = aObject.LocalToGlobal(Vector2.zero);
```

(Note that the screen mentioned here refers to the screen in FairyGUI semantics, with the upper left corner of the screen as the origin, not the screen in Unity semantics)

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
Vector2 posInB = aObject.TransformPoint(new Vector2(10,10), bObject);
```

## Conversion with world space coordinates

If you want to convert the coordinates of the world space to the coordinates in the UI, you need to convert the coordinates of the world space to the screen coordinates first, and then continue to convert, for example:

```csharp
Vector3 screenPos = Camera.main.WorldToScreenPoint (worldPos);
    // origin position conversion
    screenPos.y = Screen.height-screenPos.y;
    Vector2 pt = GRoot.inst.GlobalToLocal (screenPos);
```

If you want to convert the coordinates in the UI to the coordinates in the world space, you need to convert the coordinates in the UI to the screen coordinates before continuing the conversion, for example:

```csharp
Vector2 screenPos = GRoot.inst.LocalToGlobal (pos);
    // origin position conversion
    screenPos.y = Screen.height-screenPos.y;
    In general, you also need to provide the parameter of the distance length from the front of the camera's field of view as screenPos.z (if needed, change the screenPos to Vector3 type)
    Vector3 worldPos = Camera.main.ScreenToWorldPoint (screenPos);
```
