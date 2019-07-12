---
title: Binary Package Format
type: guide_editor
order: 230
---

The editor has supported the new release format since the 3.9.0 release, which is the binary format (hereafter referred to as the new format) *(Note: AS3/Starling/Haxe version won't provide support for the new format, please ignore this article)*. Unlike the XML format used in the past (hereafter referred to as the old format), the binary format is more compact, faster to parse, and uses less memory.

**(Note: just refers to the format after the release, the file format in the editor won't change)**

The new format compares to the old format and the upgrades that can be obtained in different situations are:
- `AddPackage`: GC can be reduced by up to 90%, CPU can be reduced by up to 80%
- `CreateObject`: GC can be reduced by up to 15%, CPU can be reduced by up to 20%

For the Unity engine, using the new format requires FairyGUI-Unity 3.0 or above, and other engines try to update to the nearest library. After the editor is updated to version 3.9.0, in the global settings in the release dialog, check `"Use Binary Format"`.

**File Naming**

The file name of the new format is different from the old format. The "@" is used as the connection character in the file name of the old format. Since many users reflect that this character isn't allowed in some cases, the new format is changed to use the "_" character. In the old format, Unity's description file name is `"xxx.bytes"`, and in the new format, it is changed to `"xxx_fui.bytes"`, in order to recognize the package file more quickly. The new format has only one description file and one or more texture and sound files, and no separate sprites are generated.

SDKs that support the new format no longer support reading files in the old format. Similarly, the old SDK doesn't recognize files in the new format and cannot be seamlessly switched. You need to make a decision.

**UI Package Management**

After using the new format, the management strategy of the UI package has changed, mainly:
1. Now `AddPackage` doesn't load all the resources (maps, sounds) in one go, but uses them again. If you need to load all, call `UIPackage.LoadAllAssets`.

2. If the `UIPackage` is loaded from the `AssetBundle`, the `AssetBundle` will be `Unload(true)` when the `RemovePackage` is used. If you confirm that all resources have been loaded (for example, `LoadAllAssets` is called), you can also uninstall the `AssetBundle` yourself.

3. Call `UIPackage.UnloadAssets` to release only the resources of the UI package without removing the package, and you don't need to uninstall the UI interface created from the UI package (these interfaces can still call the display, no error is reported, but the image is blank). When the package needs to be restored, call `UIPackage.ReloadAssets` to restore the resources. The UI interface created from this UI package can automatically restore the normal display. If the package is loaded from the `AssetBundle`, the `AssetBundle` will be Unload(true) in `UnloadAssets`, so in `ReloadAssets`, you must call `ReloadAssets` with a parameter overload and provide a new `AssetBundle` instance.

4. The Egret version doesn't currently support `Unload` and `Reload`; the Laya version supports `Unload`, but doesn't need to call `Reload`. Once the texture resource is used, it will automatically `Reload`.

5. After using the new format, the memory occupied by the UI package description is drastically reduced (only tens of KB instead of several hundred KB), so you can save the package, load, and unload without directly managing the `UIPackage`, and then use `UnloadAssets` and `ReloadAssets` to manage memory (of course this is only a suggestion).

6. When running in the Unity editor, even if the UI package isn't placed under Resources, it can be loaded normally, and the path can be full path. Of course, it is impossible to load after packaging the APP. You need to use macros to make a difference. E.g:
  ```csharp
    // Loaded directly from the file path in the editor environment for testing, the official environment uses `AssetBundle`
    #if UNITY_EDITOR
        UIPackage.AddPackage("Assets/SomePath/Package1");
    #else
        UIPackage.AddPackage(ab);
    #endif
  ```
  Similarly, `UIPanel` can also select packages in any path, and it can be displayed normally under the editor. The runtime needs to ensure that the package is loaded before the `UIPanel` is activated.

**Other API Changes**

1. [Unity] `UIConfig.buttonSound` originally accepted an `AudioClip` object, now you need to use `NAudioClip`, you can use the new `NAudioClip` (audioClip) package. `UIPacakge.GetItemAsset` (sound item) now returns the `NAudioClip` object.

2. [Unity] `UIPackage.AddPackage` accepts the overload of the `AssetBundle` parameter and no longer provides the `unloadBundleAfterLoaded` parameter. `AssetBundle` won't be uninstalled immediately after `AddPackage`.

3. To get the `Custom Data` set in the editor's built-in component design panel, you need to use the newly added API: `GComponent.baseUserData`; the method to get the custom data defined by the component in the editor remains unchanged: `GObject.data`.
