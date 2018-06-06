---
PG_TITLE: How To Use Babylon 3D GUI
---

# How To Use Babylon 3D GUI

The Babylon.js 3D GUI library is an extension you can use to generate 3D interactive user interface.

The latest version can be found here: https://github.com/BabylonJS/Babylon.js/tree/master/dist/preview%20release/gui.

And the source code is available on the main Babylon.js repo: https://github.com/BabylonJS/Babylon.js/tree/master/gui.

## Introduction
Babylon.GUI uses a meshes to create an interactive user interface which is fully integrated in your scene.

## GUI3DManager
To begin with 3D GUI, you need to instantiate a `GUI3DManager` which will be responsible for connecting all controls together:

```
var manager = new BABYLON.GUI.GUI3DManager(scene);
```

The manager only requires the scene to work on. Once instantiated, the manager will create an utility layer which is a specific child scene which will host all meshes used to render the controls. This way, your main scene won't get poluated by the utility meshes.

You can reach the utility layer with `manager.utilityLayer`.

Once you have a manager, you can start adding controls with `manager.addControl(control)`. All controls will be added to the `manager.rootContainer` container.

Please also note that the following functions are available:
- `containsControl()`: Gets a boolean indicating if the given control is in the root child list
- `removeControl()`: Removes a control from the root child list

## Containers

A container is used to organize controls in the scene. The base class for all containers is the `Container3D` class. The `manager.rootContainer` is a `Container3D` object.

All containers provide the following functions to handle controls:
- `addControl()`: Adds a control to the children of this control
- `containsControl()`: Gets a boolean indicating if the given control is in the root child list
- `removeControl()`: Removes a control from the root child list

By default, all containers will update their layout everytime you add a new control to it. But you can optimize this behavior if you plan to add multiple controls in a row with `container.blockLayout = true`:

```
panel.blockLayout = true;
for (var index = 0; index < 30; index++) {
   var button = new BABYLON.GUI.Button3D("click me");
   panel.addControl(button); 
}
panel.blockLayout = false;
```

The `Container3D` class will do nothing regarding layout of its controls. You need to use one of its children to get a specialized layout mechanism.

All specialized containers must implement the following function to provide layout mechanism:
- `_arrangeChildren()`: This function will be called everytime a new control is added. This is where children class can decide how to organize controls

### StackPanel
 The `StackPanel` container can be used to stack items either horizontally or vertically:

 ```
 var panel = new BABYLON.GUI.StackPanel3D();
 panel.isVertical = true;
 ```

The panel will automatically arrange its content every time you add a new control.

You can specify the distance between elements with `panel.margin = 0.02`.

Demo [here](https://www.babylonjs-playground.com/#HJZBRG#0)

## Controls
All controls inherit from the `Control3D` class which provides a set of basic features:

- `position`: Gets or sets the control position in world space
- `scaling`: Gets or sets the control scaling  in world space 
- `parent`: Gets or sets the parent container
- `isVisible`: Gets or sets a boolean indicating if the control is visible
- `node`: Gets the transform node used by this control
- `mesh`: Gets the mesh used to render this control

You can attach a control to a mesh or tranform node from your scene with:
```
control.linkToTransformNode(anchor);
```
This way the control will always follow the linked node or mesh. Please note that in this case, the `position` and `scaling` properties are considered local to the new parent node or mesh.
When linking a control to a transform node, please make sure that the control was first added to a container or to the root manager.

Some observables are also available to help tracking control state:
- `onPointerEnterObservable`: An event triggered when pointer enters the control
- `onPointerOutObservable`: An event triggered when the pointer move out of the control
- `onPointerDownObservable`: An event triggered when the pointer taps the control
- `onPointerUpObservable`: An event triggered when pointer is up
- `onPointerClickObservable`: An event triggered when a control is clicked on (with a mouse)
- `onPointerMoveObservable`: An event triggered when the pointer move over the control

All controls can also be the target of [behaviors ](http://doc.babylonjs.com/features/behaviour) so they expose the associated properties and functions:
- `behaviors`: Gets the list of attached behaviors
- `addBehavior()`: Attach a behavior to the control
- `removeBehavior()`: Remove an attached behavior
- `getBehaviorByName()`: Gets an attached behavior by name

All controls can also define a callback when specific event is happening. These callbacks will be called to let the user defines an animation for the control. Here is the list of available callbakcs:
- `pointerEnterAnimation`: Callback used to start pointer enter animation
- `pointerOutAnimation`: Callback used to start pointer out animation
- `pointerDownAnimation`: Callback used to start pointer down animation
- `pointerUpAnimation`: Callback used to start pointer up animation

All these callbacks are empty by default and will be implemented by specialized controls.

### Button3D

`Button3D` is a class used to create 3D buttons.

A button is a control with default animations for enter/out/down and up events.
It is based on a 2D GUI content.

You can specify the content through the `content` property and set it to any regular [2D GUI content](http://doc.babylonjs.com/how_to/gui):

```
var button = new BABYLON.GUI.Button3D("reset");

var text = new BABYLON.GUI.TextBlock();
text.text = "reset";
text.color = "white";
text.fontSize = 24;
button.content = text; 
```

By default the `Button3D` control uses a 512x512 AdvancedDynamicTexture to render its content.
You can use the following properties to change the texture resolution:
- `contentResolution`: Gets or sets the texture resolution used to render content (512 by default)
- `contentScaleRatio`: Gets or sets the texture scale ratio used to render content (2 by default)

Demo [here](https://www.babylonjs-playground.com/#2YZFA0#0)

### HolographicButton

The `HolographicButton` is a specialized button that mimics the [MRTK holographic button](https://github.com/Microsoft/MixedRealityToolkit-Unity/blob/master/Assets/HoloToolkit-Examples/UX/Readme/README_InteractableObjectExample.md#holographic-button).

An `HolographicButton` can be defined with two properties:
- `text`: Gets or sets text for the button
- `imageUrl`: Gets or sets the image url for the button

![Babylon.GUI](/img/how_to/gui/MRTK_HolographicButton.jpg)

Demo [here](https://www.babylonjs-playground.com/#2YZFA0#2)

Please note that you can overwrite the default content but you need to do it after the call to addControl:

```
var button = new BABYLON.GUI.HolographicButton("reset");
panel.addControl(button);

// Must be done AFTER addControl in order to overwrite the default content
var text1 = new BABYLON.GUI.TextBlock();
text1.text = "Reset";
text1.color = "Red";
text1.fontSize = 48;
button.content = text1;  
```

### Custom controls

You can create your own custom control by inheriting from the `Control3D` class and implementing the following functions:

- `_createNode()`: Called on controls to create a transform node or a mesh to represent the control
- `_affectMaterial()`: Called on controls to prepare and affect a material if a mesh is used to represent the control





