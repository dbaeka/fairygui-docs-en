---
title: Release Notes
type: release_notes
order: 0
---

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