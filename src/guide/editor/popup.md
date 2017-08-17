---
title: Popup Manage
type: guide_editor
order: 200
---

在UI系统中我们经常需要弹出一些组件，这些组件在用户点击空白地方的情况下就会自动消失。GRoot提供了几个API实现这类Popup行为。

- `ShowPopup` 弹出一个组件，如果指定了目标，则会调整弹出的位置到目标的下方，形成一个下拉的效果。同时提供了参数可以用来指定是向上弹出或者向下弹出。FairyGUI会根据组件的大小自动计算弹出位置，以确保组件显示不会超出屏幕。例如：

```csharp
	//弹出在当前鼠标位置
	GRoot.inst.ShowPopup(aComponent);

	//弹出在aButton的下方
	GRoot.inst.ShowPopup(aComponent, aButton);

	//弹出在自定义的位置
	GRoot.inst.ShowPopup(aComponent);
	aComponent.SetXY(100, 100);
```

窗口也可以通过ShowPopup弹出，这样弹出的窗口也具有了点击空白关闭的特性：

```csharp
	Window aWindow;
	GRoot.inst.ShowPopup(aWindow);
```

- `HidePopup` 默认情况下，用户点击空白地方就会自动关闭弹出的组件。也可以调用此API手工关闭。不指定参数时，所有当前的弹出都关闭。

