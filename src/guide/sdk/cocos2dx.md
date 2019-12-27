---
title: Cocos2dx
type: guide_sdk
order: 3
---

## Run Demo

1. Download FairyGUI-cocos2dx from GitHub Clone or directly.
2. Download the 3.x version of cocos2dx and name it cocos2d and place it in the Example root directory. The cocos2dx source code needs a change to compile. Open 2d / CCLabel.h, about 672 lines, and add the virtual modifier to the updateBMFontScale function. which is

```
virtual void updateBMFontScale();
```

3. Use Visual Studio to open Examples / proj.win32 / Examples.sln and run Demo directly.
4. Demo UI project is under Examples / UIProject. Combining UI project and example code, you can learn how to use FairyGUI under Cocos2dx.

## Add FairyGUI to your project

Add libfairygui to your WorkSpace and add references. libfairygui is a static library that is finally linked into your program.

## About GRoot

GRoot is the root object of the UI. We can create a GRoot object for each scene like Demo. GRoot :: getInstance () (or simply use the UIRoot macro) points to the last GRoot object created, which is generally the GRoot object contained in the current Scene. When Cocos2dx switches Scenes, everything in the Scene will be destroyed, including GRoot added to the Scene. If you want to avoid repeated creation of the interface, a GRoot is used in different Scenes, then you can retain GRoot yourself. After switching scenes, you can add GRoot to Scene by yourself.

## Event mechanism

Event related APIs are

```csharp
void addEventListener(int eventType, const EventCallback& callback);
    void addEventListener(int eventType, const EventCallback& callback, const EventTag& tag);
    void removeEventListener(int eventType);
    void removeEventListener(int eventType, const EventTag& tag);
    void removeEventListeners();
```

`eventType`An event type constant, defined in event / UIEventType.h.`callback`The callback function can be defined with the CC_CALLBACK_1 macro or a Lambda expression.`tag`If the remove operation is to be performed after the event registration, then the event must be identified when adding. The mechanism here is that the caller provides an EventTag. EventTag can be constructed using integers or a pointer address, for example, this can be passed directly. In particular, 0 means that there is no EventTag, that is, do not use 0 as a special EventTag.

## Lua Binding

FairyGUI-cocos2dx only provides C ++ version, Lua Binding needs to be completed by you. Or refer to online materials, such as:

1. [Cocos-taking](https://github.com/zhongfq/cocos-lua)
2. [FairyGUI multi-platform access and lua binding under Cocos2d-x](https://www.jianshu.com/p/547e584e05d8)