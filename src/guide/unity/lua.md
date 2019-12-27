---
title: Lua
type: guide_unity
order: 80
---

## Ready to work

1. **If you are using ToLua, add Scripting Define Symbols** `FAIRYGUI_TOLUA`。 XLUA is not required.
2. In the case of ToLua, add the following statement to the appropriate place in CustomSettings.cs and then regenerate the binding file.

```csharp
  _GT(typeof(EventContext)),
  _GT(typeof(EventDispatcher)),
  _GT(typeof(EventListener)),
  _GT(typeof(InputEvent)),
  _GT(typeof(DisplayObject)),
  _GT(typeof(Container)),
  _GT(typeof(Stage)),
  _GT(typeof(FairyGUI.Controller)),
  _GT(typeof(GObject)),
  _GT(typeof(GGraph)),
  _GT(typeof(GGroup)),
  _GT(typeof(GImage)),
  _GT(typeof(GLoader)),
  _GT(typeof(GMovieClip)),
  _GT(typeof(TextFormat)),
  _GT(typeof(GTextField)),
  _GT(typeof(GRichTextField)),
  _GT(typeof(GTextInput)),
  _GT(typeof(GComponent)),
  _GT(typeof(GList)),
  _GT(typeof(GRoot)),
  _GT(typeof(GLabel)),
  _GT(typeof(GButton)),
  _GT(typeof(GComboBox)),
  _GT(typeof(GProgressBar)),
  _GT(typeof(GSlider)),
  _GT(typeof(PopupMenu)),
  _GT(typeof(ScrollPane)),
  _GT(typeof(Transition)),
  _GT(typeof(UIPackage)),
  _GT(typeof(Window)),
  _GT(typeof(GObjectPool)),
  _GT(typeof(Relations)),
  _GT(typeof(RelationType)),
  _GT(typeof(Timers)),
  _GT(typeof(GTween)),
  _GT(typeof(GTweener)),
  _GT(typeof(EaseType)),
  _GT(typeof(TweenValue)),
  _GT(typeof(UIObjectFactory)),
```

3. Put FairyGUI.lua into your lua file directory.

## Listening for events

1. Common method listening and deleting listening

```csharp
require 'FairyGUI'
  
  function OnClick()
  	print('you click')
  end
  
  --也可以带上事件参数
  function OnClick(context)
  	print('you click'..context.sender)
  end
  
  UIPackage.AddPackage('Demo')
  local view = UIPackage.CreateObject('Demo', 'DemoMain')
  GRoot.inst:AddChild(view)
  
  view.onClick:Add(OnClick)
  --view.onClick:Remove(OnClick)
  --view.onClick:Set(OnClick)
```

2. **ToLua supports callback with self**

```csharp
function TestClass:OnClick()
  	print('you click')
  end
  
  function TestClass:OnClick(context)
  	print('you click'..context.sender)
  end
  
  self.view.onClick:Add(TestClass.OnClick, self)
  self.view.onClick:Remove(TestClass.OnClick, self)
```

## Using the Window class

The Window class provided by FairyGUI generally needs to be extended by developers, such as covering OnShown, OnHide, etc. In Lua, the method for writing Window extensions is:

```csharp
WindowBase = fgui.window_class ()
    
    --Build function
    function WindowBase: ctor ()
    end
    
    --Overridable functions (optional, not necessarily required)
    function WindowBase: OnInit ()
        self.contentPane = UIPackage.CreateObject ("Basics", "WindowA");
    end
    
    function WindowBase: OnShown ()
    end
    
    function WindowBase: OnHide ()
    end
    
    function WindowBase: DoShowAnimation ()
        self: OnShown ();
    end
    
    function WindowBase: DoHideAnimation ()
        self: HideImmediately ();
    end

    --Create and display window
    local win = WindowBase.New ();
    win: Show ();
    
    You can also continue to inherit the Window class obtained above, for example:
    MyWindow = fgui.window_class (WindowBase)
    
    Calling the parent method in the inherited class:
    function MyWindow: OnInit ()
    WindowBase.OnInit (self)
    end
```

## Custom extension

FairyGUI is available in C #`UIObjectFactory.SetPackageItemExtension`Make custom extensions. In Lua, you can do the same. Methods as below:

1. Define extension classes. Pay attention to the basic types, don't get it wrong. For example, the button is GButton, and the general component is GComponent.

```csharp
MyButton = fgui.extension_class (GButton)
  
  -Note that this is not a constructor, it is called when the component has been built

  function MyButton: ctor ()
  print (self: GetChild ('n1'))
  end
  
  -Add custom methods and fields
  function MyButton: Test ()
  print ('test')
  end
  
  local get = tolua.initget (MyButton)
  local set = tolua.initset (MyButton)
  get.myProp = function (self)
  return self._myProp
  end
  
  set.myProp = function (self, value)
  self._myProp = value
  self: GetChild ('n1'). text = value
  end
```

2. Register the extension class. Before registering any objects.

```csharp
fgui.register_extension ("ui://package name/my button", MyButton)
```

3. After completing the above two steps, any object created by the resource "My Button" can be accessed using MyButton. E.g:

```csharp
local myButton = someComponent: GetChild ("myButton") --The resource of this myButton is "My Button"
  myButton: Test ()
  myButton.myProp = 'hello'
  
  local myButton2 = UIPackage.CreateObject ("package name", "my button")
  myButton2: Test ()
  myButton2.myProp = 'world'
```
