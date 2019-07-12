---
title: Text
type: guide_editor
order: 60
---

Text is a basic FairyGUI control.

## Dynamic Font

Dynamic Font refers to rendering text directly using TTF fonts. The TTF font file may exist in the system or it may be packaged in the game.

The environment in which the FairyGUI editor runs isn't the same as the environment in which the application actually runs. For example, if you make an interface on a PC, the final interface may run on your phone. When editing the interface, the dynamic font is the font of the system environment in which the editor is used by FairyGUI. At runtime, it's the font provided by the actual running environment, such as the Android system. The font at design time can be different from the font at runtime, but it's recommended to select similar effects.

The font used by the setup editor can be set in the project properties. Runtime fonts need to be set by code:

```csharp
    // Roboto is the default font for Android 4.0 onwards
    UIConfig.defaultFont = "Roboto";
```

Set the font used in the runtime to ensure that the font also exists on the target platform, otherwise the desired effect won't be achieved. If you want to package fonts to the target platform, you need the support provided by the query engine, such as Unity, you can do this.:[../unity/font.html](../unity/font.html "Use Font")

If you want to use the TTF font in the editor, double-click the TTF file to confirm the installation to the system, so you can see the font in the font list after restarting the editor.

## Bitmap Font

There are often such designs in games: some characters that express special elements are made using pictures, for example:

![](../../images/20170801102632.png)

The FairyGUI editor supports bitmap fonts. First, let's create a font. Click the menu "Edit" -> "Create Bitmap Font", then pop up the font editing window, we drag the created digital image from the resource library into the window, and set the characters corresponding to each image, click Save, This way our fonts are set. If you want to modify the picture corresponding to each character, drag the picture back in.

The use of pictures instead of characters is very convenient for a small amount of text, but if you need to embed hundreds of words, make a picture for each word, and then set the corresponding characters for each, this workload is a bit much. FairyGUI editor supports external bitmap font creation tools such as BMFont, ShoeBox, etc. Please refer to the Internet for the use of these tools. Finally, an external fnt file will be exported using an external tool.**（Note 1: The file format should be in fnt format, not xml or json）**，You can import the font into the editor by clicking `Import Material` in the editor and selecting this fnt file.

The following are the recommended BMFont export settings:

![](../../images/20170801103821.png)

The following describes the function of the bitmap font setting interface:

![](../../images/20170801102803.png)

- `Image` The name of an image in the repository. If it's a font imported by BMFont, this field is empty.

- `Character` The character corresponding to the picture.

- `OffsetX` The offset of the character in the horizontal direction.

- `OffsetY` The offset of this character in the vertical direction. Negative numbers indicate that the characters move up, and integers indicate that the characters move down, but because the text layout is baseline alignment, the result of the down shift may be that the entire line moves down.

- `XAdvance` 一In general, the horizontal footprint of a character is determined by the width of the character picture. If the value here isn't 0, then this value is used as the horizontal footprint of the character.

- `Font Size` The font size of the bitmap font. It's valid after checking "Dynamic Size".

- `XAdvance` Unify the default horizontal footprint of all characters.

- `Dynamic Size` When checked, the text of this font can be used to set the font size to scale the character image. For example, first set the "font size" of the bitmap font to 12, and then set the font size to 24 in the text settings, then the final bitmap font will be doubled. If it isn't checked here, the bitmap font will remain the same regardless of the font size in the text settings, without scaling.

- `Allow Tint` When checked, the text of the font can be used to set the color of the character image. This color change is similar to the function of changing the color of a picture element. *(Note: Egret, Laya version doesn't currently support this feature).* If unchecked, the bitmap font will remain the same color regardless of the color of the text in the text settings.

- `Atlas` If the font is imported from BMFont, then the character image is on a texture, which corresponds to the texture resource. For display purposes only, it cannot be modified. If you want to set which atlas the font will eventually be published to, you should enter the settings window settings for this texture image.

## Instance Attributes

Click on the main toolbar![](../../images/20170801092548.png)Button to generate a text component.

![](../../images/20180705091908.png)

- `Text` Set the text content. When you need to wrap, you can do it in the editor. **Press enter directly**. You can use "\n" when you need to change lines with code assignment.

- `Font` Set the font used for the text. You don't need to set the font once for each text. In the project properties, you can set the default font used for all text in the project. The runtime is uniformly set by UIConfig.defaultFont. If you need to use bitmap fonts, drag font resources from the repository to here. If you use TTF fonts, then you need to install the fonts into the operating system, then enter the font name here, or click the `A` button next to it.

- `FontSize` Set the font size used for text. If you're using a bitmap font, you need to set the `"Allow dynamic change of font size"` for the bitmap font. The options here are valid.

- `Color` Set the text color. If you're using a bitmap font, you need to set the "Allow dynamic color change" setting for the bitmap font. The options here are valid.

- `LineGap` The pixel pitch of each line.

- `CharGap` The pixel pitch of each character. *（Some engines don't support）*

- `AutoSize`
  - `Automatic Width and Height` text doesn't wrap automatically, width and height are increased to accommodate all text.
  - `Auto Height` text uses a fixed width layout, automatically wraps after reaching the width, and grows to accommodate all text.
  - `Shrink` The text is fixed-width formatted, and the text is automatically shrunken when the width is reached, so that all text is still displayed. If the content width is less than the text width, no processing is done. **In most cases, you need to hook a single line at the same time.** *(The bitmap font should be checked by 'Allow change of font size' in the font properties to use the auto-shrink feature.)*
  - `None` Text is formatted with fixed width and height and doesn't wrap automatically. Clipped beyond the scope of the text box. *（This clipping function isn't enabled on Laya, Egret, and Unity platforms, that is, it will still be displayed when it's out of range.）*

- `Align` Sets the alignment of the text.

- `Bold` Set the text to bold.

- `Italic` Set the text to italic.

- `Underline` Set the text to underline. *(Laya platform doesn't support underline)*

- `Single Line` Set the text to a single line. Single-line text doesn't wrap automatically, and line breaks are ignored.

- `Stroke` Sets the stroke effect of the text. The stroke effect is different in each engine implementation:
  - AS3/Starling is implemented using filters;
  - Egret/Laya uses the features provided by the H5 engine itself;
  - Unity uses an extra Mesh to simulate the stroke effect. Generally, a character's Mesh contains 4 vertices (two triangles), and after the stroke effect is added to 20 vertices (ten triangles).

- `Shadow` Sets the shadow effect on the text. The shadow effect can be seen as a simplified stroke effect: the stroke is in all directions, and the shadow effect has only one direction.

- `Allow UBB syntax` to set text to support UBB syntax. UBB syntax can be used to make a single text contain multiple styles, such as font size, color, and more. Please refer to [UBB Syntax](#UBBSyntax). *(Laya/Cocos2dx version doesn't support multiple styles in plain text. If you have this requirement, please use rich text instead.)*

- `Clear text when publishing` indicates that the text is only used for editor preview purposes and will be automatically cleared when published.

- `Allow the use of templates` When checked, the text can express a text parameter using the syntax {count=100}. Please refer to [Text Template](#TextTemplate).

- `Enter` Set the text for input. After checking, click the ![](../../images/20170801144514.png) button next to the option to pop up the detailed settings for the input type text:

<center>
![](../../images/20170801144624.png)
</center>

- `Max Chars` The maximum number of characters allowed to be entered. 0 means no limit.

- `Password` When checked, the entered characters will be displayed as " * ".

- `Restrict` Limit the characters entered by the user. Generally only used on PC. Here, the syntax of different platforms is inconsistent.
  - `AS3/Starling` 参考资料 [TextField.restrict](http://help.adobe.com/zh_CN/FlashPlatform/reference/actionscript/3/flash/text/TextField.html#restrict)。
  - `Egret` 参考资料 [TextField.restrict](http://developer.egret.com/cn/apidoc/index/name/egret.TextField#restrict)。
  - `Laya` 参考资料 [Input.restrict](https://layaair.ldc.layabox.com/api/?category=Core&class=laya.display.Input#restrict)。
  - `Unity` 参考资料 [正则表达式语法](https://msdn.microsoft.com/zh-cn/library/az24scfc.aspx)。例如限制只能输入数字的表达式是:“[0-9]”。

- `KeyBoardType` Set the type of phone keyboard that pops up when you type on your phone.

- `Prompt Text` Set the display content when the input text content is empty, generally used to prompt the user what should be entered here. This prompt text can be set to a separate color or other style by using the UBB syntax (no need to check the UBB text), such as "[color=#CCCCCC]tip text[/color]" *（Egret and Laya versions don't support prompt text using UBB syntax, that is, you cannot specify the color of the prompt text.）*


## GTextField

Text supports dynamic creation, for example:

```csharp
    GTextField aTextField = new GTextField();
    aTextField.SetSize(100,100);
    aTextField.text = "Hello World";
```

Dynamically change the style of the text (font size, color, etc.), on different platforms, in a slightly different way:

```csharp
    // Unity/Cry
    TextFormat tf = aTextField.textFormat;
    tf.color = ...;
    tf.size = ...;
    aTextField.textFormat = tf;

    // Cocos2dx
    TextFormat* tf = aTextField->getTextFormat();
    tf->color = ...;
    tf->size  ...;
    aTextField->applyTextFormat();

    // Other platforms
    aTextField.color = ...;
    aTextField.fontSize = ...;
```

If you want to set the font of the text to be a bitmap font, the font name can be used directly by the font url, such as "ui://package name/font name".

## GTextInput

If the text is checked as "input", the running instance object is `GTextInput`.

The color and size of the cursor can be modified via `UIConfig.inputHighlightColor` and `UIConfig.inputCaretSize`. Note that the size of the input cursor will automatically select the most appropriate width according to the screen zoom, generally you don't need to modify it.

Input text has a notification event when the text changes:

```csharp
    // Unity/Cry
    aTextInput.onChanged.Add(onChanged);

    // AS3
    aTextInput.addEventListener(Event.CHANGE, onChanged);

    // Egret
    aTextInput.addEventListener(Event.CHANGE, this.onChanged, this);

    // Laya
    aTextInput.on(laya.events.Event.INPUT, this, this.onChanged);
```

Notification event when getting focus and losing focus:

```csharp
    // Unity
    aTextInput.onFocusIn.Add(onFocusIn);
    aTextInput.onFocusOut.Add(onFocusOut);

    // AS3
    aTextInput.addEventListener(FocusEvent.FOCUS_IN, onFocusIn);
    aTextInput.addEventListener(FocusEvent.FOCUS_OUT, onFocusOut);

    // Egret
    aTextInput.addEventListener(FocusEvent.FOCUS_IN, this.onFocusIn, this);
    aTextInput.addEventListener(FocusEvent.FOCUS_OUT, this.onFocusOut, this);

    // Laya
    aTextInput.on(laya.events.Event.FOCUS, this, this.onFocusIn);
    aTextInput.on(laya.events.Event.BLUR, this, this.onFocusOut);
```

To set the focus actively:

```csharp
    aTextInput.RequestFocus();
```

If the input text is set to a single line, an event will be dispatched when the user presses the Enter key (note that it's only valid on the PC. The keyboard on the phone isn't supported by Done, the engine generally doesn't provide interaction with the phone keyboard. interface):

```csharp
    // Unity
    aTextInput.onSubmit.Add(onSubmit);
```

## UBB Syntax

The UBB syntax supported by FairyGUI has:

- `[img]image_url[/img]` Display an image. The image_url here can be the internal url format of "ui://package name/image name" or the url of an external resource. The image is ultimately displayed via GLoader, and the ability to support external resources can be found in the GLoader documentation. Here you can't set the image size. If you need to set the image size, use HTML syntax instead.

- `[url=link_href]text[/url]` Show a hyperlink. The link_href can be obtained in the event triggered by the link click.

- `[b]text[/b]` Set the text to bold

- `[i]text[/i]` The settings file is italic.

- `[u]text[/u]` Set the text to be underlined.

- `[sup]text[/sup]` Set the text to superscript. *(Only supported by Unity platform, and cannot be previewed temporarily in the editor)*

- `[sub]text[/sub]` Set the text to a subscript. *(Only supported by Unity platform, and cannot be previewed temporarily in the editor)*

- `[color=#FFFFFF]text[/color]` Set the text color. Be sure to use hexadecimal color code. Color names like red and blue aren't supported. You can also set the text color to a gradient color using the color syntax *(only supported by the Unity platform and temporarily not previewable in the editor)*, for example:

```csharp
    // Specify two colors to indicate the up and down transition
    [color=#FFFFFF,#000000]文字[/color]

    // Specify four colors to make a left or right transition or a two-way transition
    [color=#FFFFFF,#CCCCCC,#000000,#FFFF00]文字[/color]
```

- `[font=font_face]text[/font]` Set the font for the text.

- `[size=10]text[/size]` Set the font size of the text.

- `[align=left/center/right]text[/align]` Set the horizontal alignment of the text. *(Only supported by Unity platform, and cannot be previewed temporarily in the editor)*

Nesting is supported between tags, but cross-nesting isn't supported. E.g:

```csharp
    // Allowed
    [color=#FFFFFF][size=20]text[/size][/color]

    // Not allowed
    [color=#FFFFFF][size=20]text[/color][/size]
```

For unsupported tags, such as "[tag]text[/tag]", FairyGUI doesn't parse, and this part of the content is output as it is. When the "[" character is to be used in non-UBB syntax, you can use "\\[" to escape. Note that you can directly enter "\\" when typing in the editor. You need to use "\" in the code. \\\" This way you can express backslashes. In addition, just escaping "[", no need to escape"]".

**Ordinary text doesn't support the img and url tags in the grammar, because ordinary text isn't arbitrarily arranged. To support graphics and text, use RichText instead.**

FairyGUI also provides a way to extend the UBB parser. Inherit the `UBBParser` class and register your own `TagHandler`. Please refer to the `UBBParser` source code or reference demo for specific implementation methods.

## Text Template

Use text templates to more flexibly adjust text output dynamically. For example, if you need to display "My Ingot: 100 Gold 200 Silver", then the common methods are:
1. Using a text control display, then the runtime programmer can update the entire text when changing the value. This is undoubtedly a redundant workload and isn't conducive to art and program decoupling.
2. Using four text controls to display, then the runtime programmer only needs to change the text control that displays the value, but the artwork is increased.

This requirement can be easily satisfied using the text template feature. Just put a text control in the editor, the text is "My Ingot: {jin=100}金{yin=200}Silver", then check "Use Text Template". This way the display in the editor is still "my ingot: 100 gold 200 silver", the runtime programmer only needs to execute the following code to update the value:

```csharp
    aTextField.SetVar("jin", "500").SetVar("yin", "500").FlushVars();
```

They can also be set in batches:

```csharp
    Dictionary<string, string> values;
    values["jin"] = "500";
    values["yin"] = "500";
    // Note that this method doesn't need to call FlushVars again.
    aTextField.templateVars = values;
```

If you want to dynamically enable the text template function at runtime, you don't need to set any switches, just call `SetVar` directly.
If you want to dynamically turn off the text template function at runtime, just set `templateVars` to null, ie:

```csharp
   aTextField.templateVars = null;
```

When the "{" character isn't used for the template, you can use the "\\{" method to escape. Note that you can directly enter "\\" when entering the editor. You need to use "\\\" in the code. \"This way to express a backslash. In addition, just escaping "{", you don't need to escape "}".

The text template takes precedence over UBB parsing, so the template can also be used in UBB. For example, the text is: "This is a color [color={color=#FF0000}] text[/color]", which can be easily changed dynamically. The need for text color.