---
title: Plugin
type: guide_editor
order: 39
---

The editor supports plugins. Plugins can implement these functions:
1. Display a custom view. (Professional Edition only)
2. Display the custom Inspector. (Professional Edition only)
3. Custom menu.
4. Import resources.
5. Add symbols to the stage, and set the properties of symbols on the stage. (Professional Edition only)
6. Insert custom actions into the publishing process. Such as custom publishing code.

## Develop

Currently the plugin only supports AS3 language development.

1. [](https://github.com/fairygui/FairyGUI-Editor/tree/master/plugin)Download the latest version of the plug-in interface and examples from the repository.
2. Create one using your familiar AS3 development tools**Library item**And put the downloaded plug-in interface into the project.
3. Set libs / FairyGUI-as3.swc as an external link, that is, do not package it into the final swc. This is very important.
4. The plugin entry is in the class PlugInMain.
5. `fairygui.plugin`The interface below is the interface of the old version, and the community version users can only access this part of the interface.
6. `fairygui.editor.api`The interface below is the 5.x interface, which can only be used by professional users.

## Deploy

Copy the generated plug-in swc to the plug-in directory, that is, under the software installation directory / plugins.
Click the menu "Tools-> Plugin Management", the pop-up window is as follows:

![](../../images/QQ20191210-144149.png)

If you see the name of your plugin in the list, the installation was successful.

## Debug

You can debug by outputting information to the console.

## References

1. [How to write an editor plugin?](http://ask.fairygui.com/?/question/5)
2. [FairyGUIToLua](https://github.com/ZxIce/FairyGUIToLua)