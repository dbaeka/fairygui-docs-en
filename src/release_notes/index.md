---
title: Release Notes
type: release_notes
order: 0
---

## 2020.2.1
1. Added grid line and adsorption to grid line function. It can be turned on in the panel that changes the background color of the canvas.
2. Add new plug-in environment support, you can use js or ts to write plug-ins.
   
## 2020.2.0
1. Added the function of editing time curve. In the transition effect settings of attribute control or dynamic effect editing, set the "jog function" to custom, and then you can click the "Edit curve" button to design a curve to customize the slow motion curve. (the unity SDK needs to be updated to the latest code of the repository, and other SDKs will support it later). Refer to [custom inching curve] (. / guide / Editor)/ transition.html# User defined creep curve).
2. Fixed the error of animation display when using pictures with format of "16 bit / channel" to make sequence frame animation.
3. Fixed the problem that font size could not be selected from the drop-down menu in the attribute interface of label component and button component.
   
P1 amendment
1. Optimize the multi frame copy and paste function of the timeline.
   
P2 correction
1. The first frame of dynamic effect is to play multiple nested dynamic effects, and only one nested dynamic effect can be played.
2. Fix the bug of preview exception when using "text control" and "multi language" simultaneously.
   
P3 correction
1. Fixed the problem that the plug-in was not displayed on the console when the plug-in was running.
   
## 2020.1.2
1. Add history to replace resource dialog box.
2. Modify behavior: when the plug-in throws an exception during the publishing process, the release will be interrupted instead of continuing to publish.
3. Fixed the problem of incorrect color of textmeshpro text in linear color space.
4. Fixed the bug of color difference in the published texture set.
5. Fixed the problem that the published texture set is inconsistent in WIN platform and Mac platform.
6. Fixed the bug that if the texture set contains only one image, the size is limited to a multiple of 4.
7. Fixed a bug in the display list view that the function of selecting multiple components with shift is incorrect.
8. Fixed a bug that could not input text normally after switching input method with windows key + space, or there was a bug similar to the lock of Ctrl key.
9. Fixed the bug of software flashback when importing folder.
P1 amendment
10. Fixed a bug in the interface of a large number of pictures with special blendmode.
11. Fixed the problem that the document did not refresh after batch modifying the image attributes.
12. Fixed a small problem with branch publishing. When a resource under a branch is marked as export, the resource should not be included in other branches of publishing.

## 2020.1.1
1. Add ThreeJS project type。[Source](https://github.com/fairygui/FairyGUI-threejs) [Guide](https://github.com/fairygui/FairyGUI-threejs-example)

## 2020.1.0
1. In this version, we have made a comprehensive reconstruction of the editor, replacing the underlying adobe AIR runtime of the original editor with Unity.
2. Added a new component type: 3D content loader. Different from the ordinary loader which can only load pictures and sequence frame animation, it is used to load more complex contents in 3D engine, such as skeleton animation, model, particles, etc. At present, the support of skeleton animation has been completed. Drag the skeleton animation directly into the editor.
3. The editor now supports importing font files. You can directly import font files with suffix TTF / TTC / OTF into the editor.
4. Support using SDF technology to render fonts (i.e. textmeshpro in unity).
5. The plug-in mechanism has been upgraded. Starting with the 2020 version, we use Lua as the development language for plug-ins. At the same time, AS3 is no longer supported to develop plug-ins.
6. Add the switch of color space, namely gamma color space and linear color space.
7. Now the resource library can display thumbnails of pictures.
8. Two tools have been added: "find unused resources" and "find duplicate resources".
9. Optimize the way of command line release and execution.
10. Added a rolling container setting, you can set "edge" do not crop.
11. A publishing setting is added. When the publishing method of "trunk contains all branches" is selected, you can choose whether to publish the pictures of branches to a separate texture set.
  
[Detail](https://mp.weixin.qq.com/s/8MKyJu-eoUCm7sOh7QredQ)

## 5.0.10
1. After setting the margin of the cropped image is selected in the publishing settings, for the image whose zoom mode is set to be a grid or tile, modify it to not crop. This is because there is a problem in applying the Nine Palaces after trimming.
2. Fixed the bug that only one custom extension can be added in the plugin.
3. Added "Repeat edge pixels" option in batch setting picture properties dialog.

## 5.0.9
1. Urgently fixed a bug affecting 5.0.8.

## 5.0.8
1. Fixed the bug that modifying image properties in batches is invalid.

## 5.0.7
1. Add "Automatic Save on Publish" option in "Preferences".
2. Animation added the ability to export as a scatter.
3. Fixed an issue where transition frame rate settings would not take effect after release.
4. Fixed an issue where the code hint released by Laya was missing GTree.

## 5.0.6
1. Provides advanced plug-in interface for the professional version, see details[Github address](https://github.com/fairygui/FairyGUI-Editor/tree/master/plugin).
2. The preferences have been supplemented with settings for the release button function.
3. Fixed several bugs.

## 5.0.5
1. Urgently fix the bug that the branch cannot increase resources.
2. Fixed a bug where resources referenced by custom attributes were not published when publishing.

## 5.0.4
1. Greatly optimized the speed of dependent queries.
2. Fixed a bug where some settings in the release dialog were not saved.
3. Fixed a bug where the texture set size was set to a multiple of 4.
4. Fixed the bug that the function of exporting resource packs is invalid.
5. The command line release adds the -t parameter to specify which branch.
6. "LayaAir (2.0)" has been added to the code release type option of the Laya project. If you choose this type, the released code will use the fairgui namespace instead of fgui.

## 5.0
1. Editor interface optimization, now you can customize the layout.
2. Added project branch function.
3. Added multi-language preview function.
4. Added the function of automatically selecting HD resources.
5. Added support for using SVG.
6. Added second display control and font size control.
7. Position control has added the function of recording percentage coordinates.
8. Added the function of polygon and regular polygon drawing.
9. Added dynamic guideline function.
10. Added custom component properties.
11. Transition supports recording coordinates in percentages.
12. Added "Home" function of the controller.
13. Added the ability to see which images the texture set contains.
14. Added the ability to set the default axis.
15. Added the ability to set the texture set size to a multiple of 4 when publishing.
16. Added the function of automatically cropping blank edges of images when publishing.
17. The expansion of the group and the mask of the object display are not saved to the component's xml now.
18. Removed the limit on the number of pack textures.
19. Fixed a bug where fonts in some systems did not appear in the font selection dialog.
20. Fixed a bug where the editor window would shake when moving on Mac.
21. If a tree list is added, the corresponding class is GTree. Check the "tree view" in the list properties.
22. The advanced group with layout adds three options: "Exclude Hidden Objects", "Disable Auto-Calculate Bracketing" and "Scaling Only Specified Index Elements"
23. The scroll container can be set to the "floating" property. After setting, the scroll bar does not occupy the viewport space.
24. The scroll bar has been increased to the minimum setting.
25. The slider has increased the minimum setting and the "integer input" setting, which can be used for the segmentation function.

[Details](https://mp.weixin.qq.com/s/pYEz595w0qo_MLXvskoDmA)