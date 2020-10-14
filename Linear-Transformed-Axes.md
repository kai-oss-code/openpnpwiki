## What is it?

The ReferenceLinearTransformAxis is a multi-input axis transformation. It can take the coordinates of several inut axes and create one transformed output coordinate. 

In OpenPnP the typical use case is to compensate for non-squareness of the machine, i.e. when the axes are mechanically not perfectly 90° to each other. On this page we will most just explore this problem.

However, non-squareness compensation is just one possibility out of many. Multiple ReferenceLinearTransformAxis can be combined to create a full [Affine Transformation](https://en.wikipedia.org/wiki/Affine_transformation). From the Wikipedia:

> Examples of affine transformations include translation, scaling, homothety, similarity, reflection, rotation, shear mapping, and compositions of them in any combination and sequence. 

## ReferenceLinearTransformAxis

### Create an Axis

Create an axis in the Machine Setup tab.

![Create Linear Transform Axis](https://user-images.githubusercontent.com/9963310/95999610-5a3e7e80-0e36-11eb-8503-a12c31ec4313.png)

### Properties

![ReferenceLinearTransformAxis](https://user-images.githubusercontent.com/9963310/96001507-64fa1300-0e38-11eb-8336-0e7e7c1220b6.png)

**Type** and **Name** are the same as in the [[ReferenceControllerAxis|Machine-Axes#properties]]. 

### Linear Transformation

We are handling this transformation as the **forward transformation** here, in the sense as it happens mechanically, from the coordinates on the mechanical axes to the true geometric coordinates in space. 

But of course, OpenPnP will internally derive the _reverse_ transformation, from a desired target coordinate, _back_ to the coordinates of the mechanical axes. Yes, it will have to solve the Affine Transformation Matrix. But you don't need to know anything about that, or linear matrix math in general.  

Input axes **X**, **Y**, **Z** and **Rotation** can be selected.

Each of the input coordinates taken from the input axes are then multiplied by a certain **Factor**. For the principal axis (the one corresponding to the **Type**), the **Factor** is often `1`. 

All these rows are then summed up. 

Finally, an **Offset** is added.

If this is a compensation transformation, i.e. repairing a mechanical imperfection rather than applying a genuinely wanted transformation, you can tell OpenPnP so, by switching on **Compensation?**. The switch can help it with certain optimizations and for proper simulation. 

### Use Case : Non-Squareness Compensation

No mechanical machine is _ever_ perfectly square. Many times this is irrelevant for the application at hand. However, inexpensive (DIY) machines are prone to exhibit some noticeable non-squareness that may matter for Pick&Place accuracy. So instead of buying much more expensive hardware, let's use a piece of clever software and some brains to compensate. The following illustration shows the problem and steps to solve it: 

![Non-Squareness-Compensation](https://user-images.githubusercontent.com/9963310/96003787-e488e180-0e3a-11eb-8732-0c4d06ca2b33.png)

Using a trusted precision square or a large millimeter paper, you should be able to detect the non-squareness using the camera crosshairs. 

1. Align the camera crosshairs with the horizontal line of the millimeter paper.
2. Move X as far as the line goes and carefully align the horizontal crosshair with the line, trying to keep the already aligned starting point pinned. It may need multiple passes back and forth.
3. Go to a vertical line of the millimeter paper.
4. Move in Y as far as the paper goes, stop at a known Y distance. 
5. Determine the offset in X (red arrow in the illustration).
6. Divide the offset by the distance in Y.
7. Enter this value in the Y Input Factor.
