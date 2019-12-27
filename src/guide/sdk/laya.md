---
title: LayaAir
type: guide_sdk
order: 0
---

1. Copy the FairyGUI library and the dependent rawinflate library to the bin / libs directory. (If you did n’t check it when the editor was published`Compressed description file`, Then this library is not needed).

![](../../images/20170809155135.png)

2. Copy fairygui.d.ts to the libs directory.

![](../../images/20170809155742.png)

3. Add references to the above two libraries in bin / index.js and pay attention to where they are placed.

![](../../images/20181117114842.png)

Note: FairyGUI only depends on two modules, laya.core and laya.html. Laya.ui is not required.

4. Use FairyGUI editor to complete UI editing. Please select the bin / res directory of the Laya project (other directories are also possible, of course). Get two (or more) files after posting.

![](../../images/20170809160159.png)

5. Load these two files when the program starts (or wherever you need these UIs) and complete the initialization.

```csharp
onLoaded(): void {
      Laya.stage.addChild(fgui.GRoot.inst.displayObject);
      
      fgui.UIPackage.addPackage("res/Basic");
      fgui.UIConfig.defaultFont = "宋体";
      fgui.UIConfig.verticalScrollBar = "ui://Basic/ScrollBar_VT";
      fgui.UIConfig.horizontalScrollBar = "ui://Basic/ScrollBar_HZ";
      fgui.UIConfig.popupMenu = "ui://Basic/PopupMenu";
      fgui.UIConfig.buttonSound = "ui://Basic/click";
  }
```

## Must-read game development

1. Because rawinflate has a problem on the small game platform, it is not used directly. Please use the latest editor. Uncheck "Compress description file" in the global settings of the release dialog. The rawinflate library will no longer be referenced (and no packaging is required).
2. Mini games do not support fui extensions, so in the publishing interface, you must change the extension to the extensions supported by the mini games (check the mini game documentation yourself). Then set it in the code:
```
fairygui.UIConfig.packageFileExtension = "The extension you defined";
```
3. In the publish dialog, check "Use binary format" in the global settings.
4. AddPackage has two methods, one is the traditional method of passing in the file name, and the other is to directly pass in the content of the entire file, that is, no matter where your content comes from. Two methods can be selected as required.
5. If you encounter loading failures, please check Laya's loading process. Because FairyGUI is not responsible for loading, you need to ensure that the resources have been successfully loaded before AddPackage.