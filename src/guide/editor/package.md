---
title: Package
type: guide_editor
order: 2
---

## Definition of package

FairyGUI organizes resources in packages. A package is represented as a directory in the file system. Each subdirectory under the assets directory represents a package. ** Each resource in a package has an attribute of whether to export it. A package can only be set as an exported resource using other packages, and it is not accessible if it is not set as an exported resource. At the same time, only components set to export can be dynamically created using code. ** When exported assets are displayed in the Asset Library, there is a small red dot in the lower right corner of the icon.

Each package has a package.xml file, which is the database file for the package. If this file is corrupted, the contents of the package will be unreadable. In the case of multi-person collaboration, if there is a conflict when pulling package.xml, please resolve the conflict before refreshing the package in the editor.

After the package is released, you can get a description file and one or more texture sets.**The number of files and packaging methods are different on different platforms**。

## Package dependencies

FairyGUI does not deal with the dependencies between packages. If package B exports a component B1 and component A1 of package A uses component B1, then before creating A1, you must ensure that package B has been loaded, otherwise A1 B1 cannot be displayed correctly (but it will not affect the normal operation of the program). **This loading needs to be called manually by the developer, FairyGUI will not load automatically.**

In the code, you can query the dependencies between packages through the following API:

```csharp
var dependencies = UIPackage.dependencies;
    foreach (var kv in dependencies)
    {
        Debug.Log (kv ["id"]); // id of the dependent package
        Debug.Log (kv ["name"]); // Name of the dependent package
    }
```

## Principles of dividing packages

How to divide the package, one principle is not to establish a cross-reference relationship. For example, avoid the situation where package A uses resources of package B, and package B uses resources of package C. We generally set up one or more public packages, put the resources that are frequently used by the entire project here, and put some basic components, such as buttons, scroll bars, window backgrounds, etc. here. When other packages need to be used, drag them directly from the public package. Except for public packages, other packages try not to reference each other. Concise dependencies make it easier for programmers to control the loading and unloading of UI resources.

The granularity of package division generally does not have a hard and fast rule. In specific practice, there are different schemes. For example, some people prefer to divide each module into one package. Some people prefer a smaller package, so they put together the resources and components of different UI modules. These solutions have little impact on the performance of the UI. However, try not to spread the image resources too much, because different packages of images cannot be used on the same texture set. If the resources are too scattered, the texture set may be left blank and waste space.

## Resource URL

In FairyGUI, every resource has a URL. Select a resource, Context the menu, and select "Copy URL" to get the URL address of the resource. Whether in the editor or in the code, you can reference the resource through this URL. For example, to set a button icon, you can drag it directly from the library, or you can manually paste the URL address. This URL is a string of encodings and is not readable. It is difficult to read in development, so we usually use another format: ui:// package name / resource name. The two URL formats are universal, one is unreadable but is not affected by package or resource renaming; the other is more readable.

**Note: The address in the format "ui://package name/resource name" does not include the folder, only the package name and resource name are needed.**

To obtain the URL address of the specified object at runtime, you can use the following methods:

```csharp
// URL address of the object
  Debug.Log (aObject.resourceURL);

  // The name of the object in the resource library
  Debug.Log (aObject.packageItem.name);

  // The name of the package where the object is located
  Debug.Log (aObject.packageItem.owner.name);

  // Get the resource name based on the URL
  Debug.Log (UIPackage.GetItemByURL (resourceURL) .name);
```
