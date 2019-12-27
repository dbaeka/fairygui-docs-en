---
title: Popup
type: guide_editor
order: 31
---

In the UI system, we often need to pop up some components, and these components will automatically disappear when the user clicks on a blank space. FairyGUI has this feature built in.

## Popup management

The API to pop and close Popup is provided in GRoot.

- `ShowPopup`Pop up a component. If a target is specified, the pop-up position will be adjusted below the target, forming a drop-down effect. Parameters are also provided to specify whether to pop up or pop down. FairyGUI automatically calculates the pop-up position based on the size of the component to ensure that the component display does not exceed the screen. E.g:

   ```csharp
   // popup at current mouse position
    GRoot.inst.ShowPopup (aComponent);

    // pop up below aButton
    GRoot.inst.ShowPopup (aComponent, aButton);

    // popup at custom position
    GRoot.inst.ShowPopup (aComponent);
    aComponent.SetXY (100, 100);
   ```

   The window can also be popped up through ShowPopup, so the pop-up window also has the feature of clicking blank to close:

   ```csharp
   Window aWindow;
    GRoot.inst.ShowPopup (aWindow);

    // The only difference from using aWindow.Show to display the window is that it has the function of clicking blank to close, there is no difference in other usage.
   ```

- `HidePopup`By default, the pop-up component is automatically closed when the user clicks on a blank space. You can also call this API to close it manually. You can specify the Popup that needs to be closed. When no parameter is specified, all current popups are closed.

The pop-up box will automatically close when you click on the blank space. If you want to be notified of this shutdown, you can listen to the event of moving out of the stage, for example:

```csharp
//Unity/Cry
    aComponent.onRemoveFromStage.Add(onPopupClosed);

    //AS3
    aComponent.addEventListener(Event.REMOVED_FROM_STAGE, onPopupClosed);

    //Egret
    aComponent.addEventListener(egret.Event.REMOVED_FROM_STAGE, this.onPopupClosed, this);

    //Laya
    aComponent.on(laya.events.Event.UNDISPLAY, this, this.onPopupClosed);

    //Cocos2dx
    aComponent->addEventListener(UIEventType::Exit, CC_CALLBACK_1(AClass::onPopupClosed, this));

    //CocosCreator
    aComponent.on(fgui.Event.UNDISPLAY, this.onPopupClosed, this);
```

## PopupMenu

PopupMenu is a tool class provided by FairyGUI for implementing popup menus. First you need to make a menu component in the editor, click "Resources-> New Popup Menu ...", and then follow the wizard to complete. The key element in the menu component is named`list`For the list component, the overflow processing mode of the list should be selected as visible, because in general, the menu displays all items without scrolling.

After making the menu component, this menu can be generated and called in the code.

First set the global menu resources:
```csharp
UIConfig.popupMenu = "ui://package name/menu component name";
    
    // if there is a design divider
    UIConfig.popupMenu_seperator = "ui://package name/menu separator";
```

```csharp
// If the constructor takes no parameters, it means using the resources defined in UIConfig.popupMenu.
    / / Can also take a parameter, specify the menu component resources used by this menu
    PopupMenu menu = new PopupMenu ();

    // If you want to modify the width of the menu.
    menu.contentPane.width = 300;

    // Add a menu item and register the click callback function
    GButton item = menu.AddItem ("Title", MenuItemCallback);
    item.name = "item1";

    // click callback function, context.data is the item that is currently clicked
    void MenuItemCallback (EventContext context)
    {
        GButton item = (GButton) context.data);
        Debug.Log (item.name);
    }

    // CocosCreator version callback function, the first parameter is the clicked item
    MenuItemCallback (item, evt) {
    }

    // Add divider
    menu.AddSeperator ();

    // Setting menu items are grayed out
    menu.SetItemGrayed ("item1", true);
```
