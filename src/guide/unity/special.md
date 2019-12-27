---
title: Special
type: guide_unity
order: 90
---

## PaintMode

FairyGUI can make a display object enter`Painting mode`In simple terms, the target object is drawn on a texture, and then the texture can be manipulated to achieve some special effects. The following functions all rely on this model:

- The BlendMode of the component is not the default value "Normal".
- Use arbitrary filters on components;
- Use blur filters on arbitrary objects;
- Component is tilted;
- The 2D simulation 3D perspective function is used, that is, GObject.perspective = true;
- Curved UI

The painting mode feature relies on an additional camera and two layers that are specifically defined. Camera is**Automatic generated**Yes, the name is CaptureCamera, you don't need to access and manipulate it. The names of the two layers are```FUNNY 和 Hidden FUNNY`If these two layers are not defined in Unity, a warning will appear:

```
Please define two layers named 'VUI' and 'Hidden VUI' "
```

At this point you should add them to the Layer definition to avoid this warning. These two Layers can be freely defined to the unused layer serial number (the default is 30 and 31), but pay attention to the Culling Mask of all cameras (except CaptureCamera, which is automatic, regardless of)**Not choose**These two layers.

In addition to the functions mentioned above, the object will enter the painting mode, and it can also be triggered manually by calling the API:

```csharp
// Enter painting mode
    EnterPaintingMode (int requestorId, Margin? margin);

    // Leave painting mode
    LeavePaintingMode (int requestorId);
```

- `requestorId`Requester id. When multiple requests require the display object to enter painting mode, this id can be used to distinguish. The values are 1, 2, 4, 8, 16 and so on. Internal hold within 1024. The user-defined id starts from 1024.
- `margin`Leave blank around the texture. If the content after special processing is larger than the original content, then the setting here can make the texture larger.

**If the target object uses a custom mask, additional settings are required to display properly.**

```csharp
UIConfig.depthSupportForPaitingMode = true;
```

## Component screenshot

Use the following methods to achieve the function of screenshots of components. The principle is provided by FairyGUI`Painting mode`Features.

```csharp
GObject aObject;
    DisplayObject dObject = aObject.displayObject;
    dObject.EnterPaintingMode (1024, null);

    // The texture will not be updated until this frame is rendered, so the code that accesses the texture needs to be delayed until the next frame is executed.
    yield return null;

    RenderTexture tex = (RenderTexture) dObject.paintingGraphics.texture.nativeTexture;
    // After getting tex, you can use Unity's method to save as an image or do other processing. The specific treatment is omitted.

    // End the painting mode after the processing ends. The id must correspond to the Enter method.
    dObject.LeavePaintingMode (1024);
```

## Emoji display and input

FairyGUI supports emoticon display and direct input, that is, the emoticon image is displayed in the input box directly in the input state. It supports input on the PC and also supports input from the phone's native keyboard. E.g:

![](../../images/20170924151030.png)

The usage method is to define the emojies collection for rich text or input text.

```csharp
Dictionay <uint, Emoji> emojies = new Dictionary <uint, Emoji> ();
    // unicodeValue is the unicode encoding of the character, imageURL is the image path
    emojies.Add (unicodeValue, new Emoji (imageURL));

    GRichTextField richTextField;
    richTextField.emojies = emojies;

    GTextInput textInput;
    textInput.emojies = emojies;
```

Each expression corresponds to a Unicode encoding. There are two types of expressions, one is a custom expression, and the other is the expression that comes with the phone's keyboard.

For a custom expression, you can use any character as the code for the expression, and you can select some characters that are hardly input by the user directly.

For expressions on the keyboard of mobile phones, UCS32 encoding is generally used, that is, 4-byte Unicode encoding. This is different from the UTF8 or UCS16 we usually use. Generally, the characters we use in the code, whether English or Chinese, can be expressed using a char, but the 4-byte Unicode encoding requires two char expressions in C #, which is called Surrogate Pair. Losing any char will cause encoding errors.

For example, the expression corresponding to the Unicode encoding 0x1f600 is:![](../../images/20170924153658.png)

Register this expression:

```csharp
emojies.Add(0x1f600, new Emoji("ui://Emojies/1f600"));
```

Common Unicode encoding and emoji image resources on IOS can be downloaded from here:[http://res.fairygui.com/ios-emoji.zip](http://res.fairygui.com/ios-emoji.zip "ios-emoji.zip")

It should be noted that the Unicode encoding of 0x1f600 is expressed by two chars in C #, that is, "\ U0001f600", but it does not mean that the integer values of these two chars are 0x1 and 0xf600. If you need to transfer text or database storage to UCS32 encoded text, you need to ensure that your network layer or database interface supports this encoding. You can get the detailed processing method of this encoding in Baidu. The keyword is "ios emoticon encoding".

The text component that comes with UGUI has no processing of expressions, so Unity blocks the input of expressions. If you want to turn on this function, you need to modify the /Classes/UI/Keyboard.mm file in the Xcode project and change the FILTER_EMOJIS_IOS_KEYBOARD macro to 0.

## Arabic language text display

FairyGUI supports Arabic language text display from right to left. If you need to turn on this function and use the source version, you need to add RTL_TEXT_SUPPORT in the Scripting Define Symbols of Unity Player Settings; if you use the DLL version, please compile the DLL that contains this function yourself, or ask the owner

![](../../images/20180319022527.png)

## UI Automation Test

FairyGUI provides automated testing tools[AirTest](http://airtest.netease.com/)SDK for easy access to automated testing. [](https://github.com/AirtestProject/Poco-SDK/tree/master/Unity3D)You can download it from here. Please pay attention to fill in FAIRYGUI_TEST in Scripting Define Symbols when using.
