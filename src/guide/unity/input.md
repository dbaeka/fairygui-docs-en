---
title: Input processing
type: guide_unity
order: 30
---

## 鼠标/触摸输入

FairyGUI使用内置的机制进行鼠标和触摸事件的处理，不使用射线。如果要使用射线，可以将UIPanel的“HitTest Mode”设置为“Raycast”。

如果要区分点击UI还是点击场景里的对象，可以使用下面的方法：

```csharp
    if(Stage.isTouchOnUI) //点在了UI上
    {
    }
    else //没有点在UI上
    {
    }
```

这种检测不仅适用于点击，也适用于悬停。例如，如果鼠标悬停在UI上，这个判断也是真。

在任何事件回调中都可以获得当前鼠标或手指位置，例如：

```csharp
    void EventHandler(EventContext context)
    {
        Debug.Log(context.inputEvent.x + ", " + context.inputEvent.y);
    }
```

如果不在事件中，需要获得当前鼠标或者手指的位置，可以用：

```csharp
    //这是鼠标的位置，或者最后一个手指的位置
    Vector2 pos1 = Stage.inst.touchPosition;

    //获取指定手指的位置，参数是手指id
    Vector2 pos2 = Stage.inst.GetTouchPosition(1);

    //这是当前按下的手指的数量
    int touchCount = Stage.inst.touchCount;

    //获得当前所有按下的手指
    int[] touchIDs = Stage.inst.GetAllTouch(null);
```

如果要侦听鼠标或者手指的移动，FairyGUI提供了一个全局的事件。注意：在需要时侦听，不需要时请移除。

```csharp
    Stage.inst.onTouchMove.Add(onTouchMove);
```

## VR输入处理

VR里输入一般使用凝视输入，或者手柄输入，针对这些新的输入方式，FairyGUI提供了封装支持，也就是说，在VR应用里，你仍然可以像处理鼠标或者触摸输入一样处理VR的输入。

首先，需要把这些外部输入传入FairyGUI。在Stage类里提供了这些API：

```csharp
	public void SetCustomInput(ref RaycastHit hit, bool buttonDown);
	public void SetCustomInput(ref RaycastHit hit, bool buttonDown, bool buttonUp);
```

- `hit` 没有手柄的，这里传入眼睛的射线（其实就是摄像机的射线）击中的目标；有手柄的，传入手柄射线击中的目标。

- `buttonDown` 是否有按键按下。对于没有buttonUp参数的API，系统会在下一帧自动模拟一个buttonUp。

- `buttonUp` 是否有按键松开。按下、松开整个过程构成一次点击。如果只有按下没有松开，则不会有点击触发，必须注意这一点。

SetCustomInput可以放在Update里调用，而且必须**每帧调用**。如果使用了SetCustomInput，则FairyGUI不再处理鼠标或者触摸出入。

通过这种方式处理VR输入，UI逻辑不需要做任何修改。

## 键盘输入

侦听键盘输入的方法是：

```csharp
    Stage.inst.onKeyDown.Add(OnKeyDown);

    void OnKeyDown(EventContext context)
    {
        Debug.Log(context.inputEvent.keyCode);
    }
```

在手机上是通过原生的键盘输入。键盘弹出时，派发GTextInput.onFocusIn事件，键盘收回时，派发GTextInput.onFocusOut事件。

Unity在键盘输入时自带了一个额外的输入框，如果你不需要这个输入框，希望像微信那样弹出自己的输入框，你需要自行编写原生支持，FairyGUI这边提供的支持有：

```csharp
    //取消Unity弹出原生的键盘的行为
    Stage.keyboardInput = false;

    //插入字符到光标位置
    Stage.inst.InputString("xxx");
```

**复制粘贴问题**

当使用DLL形式的插件时，因为DLL默认是为移动平台编译的，所以不支持复制粘贴（如果要支持，需要自己写原生）。如果是在PC平台上使用时，需要将[CopyPastePatch.cs](https://github.com/fairygui/FairyGUI-unity/blob/master/Examples.Unity5/Assets/FairyGUI/CopyPastePatch.cs)放到工程里，并在游戏启动时调用CopyPastePatch.Apply()，就可以在PC平台激活复制粘贴功能。如果你是使用源码形式的插件，不需要进行这个处理。

## 手势

FairyGUI提供了常用手势的支持，它们是：

- `LongPressGesture` 长按手势。

- `SwipeGesture` 手指划动手势。

- `PinchGesture` 两指缩放手势。

- `RotationGesture` 两指旋转手势。

