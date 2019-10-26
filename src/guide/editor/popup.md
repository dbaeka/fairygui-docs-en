---
title: Popup
type: guide_editor
order: 200
---

In the UI system we often need to pop up some components that will automatically disappear when the user clicks on a blank place. FairyGUI has this feature built-in.

## Popup Management

The show and close APIs for `Popup` are available in `GRoot`.

- `ShowPopup` pops up a component. If a target is specified, the pop-up position is adjusted below the target to form a drop-down effect. At the same time, parameters are provided to specify whether to pop up or pop down. FairyGUI automatically calculates the pop-up position based on the size of the component to ensure that the component display doesn't exceed the screen. E.g:

```csharp
    // Pop up at the current mouse position
    GRoot.inst.ShowPopup(aComponent);

    // Pop up under the aButton
    GRoot.inst.ShowPopup(aComponent, aButton);

    // Pop up in a custom location
    GRoot.inst.ShowPopup(aComponent);
    aComponent.SetXY(100, 100);
```

The window can also be popped up via `ShowPopup`, so that the pop-up window also has the feature of clicking the blank to close:

```csharp
    Window aWindow;
    GRoot.inst.ShowPopup(aWindow);

    // The only difference between using the aWindow.Show display window is that there is more click-to-blank off functionality. There is no difference in other usages.
```

- `HidePopup` By default, when a user clicks on a blank space, the pop-up component is automatically closed. You can also call this API to close it manually. You can specify the `Popup` that needs to be closed. When no parameters are specified, all current popups are closed.

After clicking in the blank space, the popup will automatically close. If you want to get this notification of closing, you can listen to events that move out of the stage, for example:

```csharp
    // Unity/Cry
    aComponent.onRemoveFromStage.Add(onPopupClosed);

    // AS3
    aComponent.addEventListener(Event.REMOVED_FROM_STAGE, onPopupClosed);

    // Egret
    aComponent.addEventListener(egret.Event.REMOVED_FROM_STAGE, this.onPopupClosed, this);

    // Laya
    aComponent.on(laya.events.Event.UNDISPLAY, this, this.onPopupClosed);

    // Cocos2dx
    aComponent->addEventListener(UIEventType::Exit, CC_CALLBACK_1(AClass::onPopupClosed, this));
```

## PopupMenu

`PopupMenu` is a tool class provided by FairyGUI for implementing pop-up menus. First you need to make a menu component in the editor. Click on `"Resources -> New Popup Menu..."` and follow the wizard. The key element in the menu component is the list component named `list`. The overflow processing mode of the list should be selected to be visible, because in general, the menus display all items and do not need to be scrolled.

Once the menu component is created, it can be generated and called in the code.

First set the global menu resources:
```csharp
    UIConfig.popupMenu = "ui://PackageName/MenuComponentName";

    // If there is a menu divider
    UIConfig.popupMenu_seperator = "ui://PackageName/MenuDivider";
```

```csharp
    // If the constructor takes no arguments, it means using the resources defined in UIConfig.popupMenu.
    // can also take a parameter, specify the menu component resources used by this menu
    PopupMenu menu = new PopupMenu();

    // Modifying the menu width
    menu.contentPane.width = 300;

    // Add a menu item and register the click callback function
    GButton item = menu.AddItem("标题", MenuItemCallback);
    item.name = "item1";

    // Click on the callback function, context.data is the currently clicked item
    void MenuItemCallback(EventContext context)
    {
        GButton item = GButton(context.data);
        Debug.Log(item.name);
    }

    // Add divider
    menu.AddSeperator();

    // Setting menu items are grayed out
    menu.SetItemGrayed("item1", true);
```