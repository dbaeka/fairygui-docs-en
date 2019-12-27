---
title: FAQ
type: guide_unity
order: 100
---

## Tips for old package formats

```
FairyGUI: old package format found in 'XXXX'
```

You used the new version of the SDK (> = 3.0), but the format exported by the editor is XML. The new version of the SDK no longer supports this format. Please select "Use Binary Format" globally in the release dialog. For details[Upgrade to binary format](../editor/upgrade_binary_format.html)

## The package has been correctly placed in the Unity project, but the package / component dialog cannot see the package and its components

Please check if the package file name is like xxx_fui. If yes, it means that you have used the binary package format, but the SDK is still an old version. Please use SDK 3.0 or above.

## UI package failed to load

```
FairyGUI: invalid package 'XXXX', 
if the files is checked out from git, 
make sure to disable autocrlf option!
```

If the version of FairyGUI editor you are using is less than 3.1.5, the format of the package description file is plain text. When this file is pulled from GIT, the file content may be changed due to GIT's automatic line break conversion function. FairyGUI recognizes errors. The solution is to turn off GIT's automatic conversion line break function, and then pull it down again.

If the version of FairyGUI editor you are using is greater than or equal to 3.1.5, then the format of the package description file has been changed to binary and is no longer affected by GIT. This error still occurs because the new format requires Unity SDK 1.8.3 or later. This error is reported if the old version does not recognize the format. The solution is to upgrade your Unity SDK, upgrade your Unity SDK, upgrade your Unity SDK, and say the important thing three times.
If you do not want to upgrade for the time being, you can modify xxx.fairy (your project description file)`version="3.1"`Change to`version="3"`So that the editor is published using the old text format.

In addition to the above error, if the package still fails to load, check whether the package name and path are correct. In particular, pay attention to the package can only be placed in the Resources directory or its subdirectories, otherwise you can only play AB packages. Let's see if the Unity project is placed in a directory with a Chinese name. This may cause the loading to fail.

## Show white screen / tips atlas not found

```
FairyGUI: texture 'atlas0.png' not found in xxx
```
Beginning in Unity 5.5, texture settings have been added[TextureShape](http://ask.fairygui.com/?/question/1)Property, just set it to 2d.

## Running error and can't see the interface, but the editing mode is OK

```
Create Component1@Package1 failed!
```
This kind of error is generally due to the use of UIPanel. The reasons may be:
1. Your UI package is not placed properly**Resources**Directory, or Resources misspelled! Too many novices make such mistakes.
2. If there are cross-package references, you need to use AddPackage to manually load dependent packages, and note that AddPackage must be placed in Awake before UIPanel is created.
3. If the location has been moved or the name has been changed after the package is released, reset the UIPanel package and component names.

## No image / text displayed, but no error

The shader of FairyGUI is not placed in the project, that is, the shader in Resources / Shaders in the plugin. Please reinstall the plugin.

## UI display is duplicated, or still displayed after UI is destroyed
1. The main camera is not placed in the scene.
2. The ClearFlags of the main camera is incorrectly set to Depth.
3. There are other cameras in the scene, and its Culling Mask setting has UI checked.

## Suddenly the text turns purple and then returns to normal

[Reference font](font.html#常见问题)。

## Hierarchy display error

If you see the level of disorder, it is mostly due to the impact of fairyBatching. in[DrawCall optimization](drawcall.html)As mentioned in this tutorial, I will explain it here.

For components with fairBatching turned on, when developers call APIs such as SetPosition to change the position, size, rotation, or scaling of child or grandchildren components, depth adjustments are not automatically triggered. For example, an image was originally displayed on the top level of a window. If you use Tween to move it from the original position to another position, the image may be obscured by other elements in the window. At this time, the developer needs to manually trigger the depth adjustment, for example:

```csharp
aObject.InvalidateBatchingState();
```

This API does not need to be called by a component with fairBatching turned on, aObject can be any of the included components. And you can call it at any time, every frame, as long as you confirm that it is needed. Its consumption is not large, but it cannot be said that it is not.

If this is observed during the transition process, set invalidateBatchingEveryFrame = true for transition.

## Image stitching / tiling

Double-click the image, and check "Repeat edge pixels" in the image properties dialog box.

## CaptureCamera appears in the scene

reference[PaintMode](special.html#PaintMode)

## A warning appears that a layer needs to be defined

```
Please define two layers named 'VUI' and 'Hidden VUI' "
```

reference[PaintMode](special.html#PaintMode)

## Drop UI to other layers

Yes, but you need to use the source version. Just change the LayerName in StageCamera.cs.

## Stage.LateUpdate function CPU usage is too high

Stage.LateUpdate not only contains the consumption of FairyGUI itself, but because it is the source of emitting events, it also includes the consumption of event processing logic. So enter Deep Profiler mode, expand Stage.LateUpdate, and check the consumption of your event handler. FairyGUI's own consumption is very small under Container.Update in non-Deep Profiler mode (Note: The data in Deep Profile can only be viewed as a percentage value, not an absolute value, because the consumption of this profile mode itself is also very big).

## DrawCall is too high

After opening FairyBatching, if you still feel that DrawCall is too high, you can use the Unity tool Frame Debugger to troubleshoot. You can see if there are too many masks (overflow hiding, scrolling), or**Unreasonable overlap between components**。 In addition, large sections of text, image tiling, using filters, and BlendMode will also block the merging of DrawCall.

## After uninstalling the UI package, I still see Assets not released in the Profiler

This may be a reference to resources such as textures by the Unity editor itself. There will be no real operating environment.

## Is there a long press event

Long press processing uses LongPressGesture.

## Can FairyGUI and UGUI coexist

Yes. Reference tutorial[Insert Model / Particle / Canvas](insert3d.html)。

## Lua cannot listen for events

```
LuaException: Delegate FairyGUI.EventCallback1 not register
```

If you confirm that Gen Delegates has been executed, then the only possible reason is that the DelegateFactory does not have init. Please check for problems with third-party frameworks.

## Does FairyGUI support XLua

No matter tolua, slua or xlua, all are C # binding schemes. FairyGUI is written in C # and does not reference any third-party libraries except system libraries and Unity libraries, so there is no such thing as not supporting Lua. An example of the combination of xlua and FairyGUI can be Baidu "FairyGUI xlua".

## Using UIConfig.buttonSoundVolume to change global button volume has no effect

UIConfig.buttonSoundVolume is only used to initialize settings, and subsequent changes are invalid. If you want to control the volume or volume of the global sound, you can do this:

```csharp
// Switch sound
    GRoot.inst.EnabledSound ();
    GRoot.inst.DisableSound ();

    // Adjust the global sound volume, this includes the button sound and the sound played by the effect
    GRoot.inst.soundVolume = 0.5f;
```

## Can FairyGUI play videos

The video playback function is provided by Unity and does not require FairyGUI support. You can use a Loader and assign the texture of the video object to the Loader.

## Can I create transition with code

Transition is a concept proposed for visual design in the editor. If you are designing UI element actions entirely in code, then you do n’t need to use dynamic effects, just use Tween to complete it. You can use DoTween or FairweGUI's built-in GTween.

## Created Tweener calls kill (false) occasionally to pause other transitions being played

GTweener is reused. Be careful to check all your code, don't reuse or misuse the GTweener instance, that is, once Tween ends, don't use the GTweener instance again, let alone kill. It is generally recommended not to save GTweener instances.
