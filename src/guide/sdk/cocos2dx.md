---
title: Cocos2dx
type: guide_sdk
order: 3
---

## Running the Demo

1. Download using GitHub Clone or directly from `FairyGUI-cocos2dx`.
2. Download the 3.x version of cocos2dx and name it cocos2d in the Example root directory. Cocos2dx source code needs to be changed in one place to compile, open `2d/CCLabel.h`, about 672 lines, and put the virtual modifier on the `updateBMFontScale` function. which is:

  ```
    virtual void updateBMFontScale();
  ```

3. Open `Examples/proj.win32/Examples.sln` using Visual Studio and run the Demo directly.
4. Demo's UI project is under `Examples/UIProject`. Combine UI engineering and example code to understand how FairyGUI is used under Cocos2dx.

## Adding FairyGUI to Your Project

Add `libfairygui` to your WorkSpace and add a reference. `Libfairygui` is a static library that is linked to your program.

## About GRoot

`GRoot` is the root object of the UI. We can create a `GRoot` object for each scene like in the Demo. `GRoot::getInstance()` (or simply use the `UIRoot` macro) points to the last created `GRoot` object, which is generally the `GRoot` object contained in the current Scene. Cocos2dx will destroy everything in Scene when switching Scene, including `GRoot` added to Scene. If you want to avoid repeated creation of the interface, by using a shared `GRoot` in different Scenes, then you can own `GRoot` retain. After switching the scene, you can add `GRoot` to Scene.

## Event System

The event API has these methods:

```csharp
    void addEventListener(int eventType, const EventCallback& callback);
    void addEventListener(int eventType, const EventCallback& callback, const EventTag& tag);
    void removeEventListener(int eventType);
    void removeEventListener(int eventType, const EventTag& tag);
    void removeEventListeners();
```

`eventType` An event type constant, defined in `event/UIEventType.h`.
`callback` The callback function can be defined with the `CC_CALLBACK_1` macro or with a Lambda expression.
`tag` If the event is to be removed after the event is registered, then this must be added to identify the event. The mechanism here is that the caller provides an `EventTag`. `EventTag` can be constructed with an integer, or a pointer address, for example, you can pass this directly. In particular, 0 means no `EventTag`; meaning, don't use 0 as a special `EventTag`.

## Lua Binding

FairyGUI-cocos2dx is only available in C++, and Lua Binding needs to be done by you. There are many ways to do this online. For example, the sharing of this enthusiastic netizen:[https://www.jianshu.com/p/547e584e05d8](https://www.jianshu.com/p/547e584e05d8 "FairyGUI在Cocos2d-x下的多平台接入和lua绑定")
