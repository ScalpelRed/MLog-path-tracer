MLog path tracer by ScalpelRed  
Machine schematic for the game will be added later

# Basic usage
When the machine is built and powered, it will wait for reset button being pressed. Once it's pressed, the machine will reset GPUs and will start rendering the scene repeatedly. First render is done with 100% opacity, all following renders are done with 50% opacity, which makes the scene smoother with each render).  
Pressing the reset button again will result in the rendered scene getting erased and rendered from 100% opacity again.

# How to edit the scene:
CPU (small processor in the middle of the machine) is responsible for scene data. Open ControlCode.mlog and copy the code into a text editor. When you're ready to load your scene into the machine, open CPU's code editor and paste it there. The CPU will wait for reset button to be pressed again.  
Note: sometimes, pressing reset results in only half of the scene being rendered or half of the scene being rendered with 50% opacity. Until this bug gets fixed, you can try pressing the reset button again when another frame is rendered partially.  
Only two shapes are supported:
- Sphere - a spherical object with XYZ position, radius and single material,
- Y-plane - a plane parallel to the ground. Y-planes have checkerboard pattern - all cells have same size, even cells use material 0, odd cells use material 1 (two materials are specified for an Y-plane)

## Data types
- Float means a number within floating-point type limitations,
- Amount means a number from 0.0 to 1.0,
- XYZ means 3 numbers within floating-point type limitations,
- RGB means 3 numbers from 0.0 to 1.0,
- UInt means an unsigned integer within floating-point type limitations.  
Setting a value outside variable type's limitations may result in undefined behavior.  
Using an index of an object that doesn't exist may result in garbage data being used as the object.

## Materials
In #Materials section, you can edit materials. Make sure the variable matCount is equal to material count.  
Materials have emission (RGB, the color that's added to ray when it bounces off the object), reflection (RGB, the color that the object will reflect) and roughness (Amount, the amount of randomness added to ray's direction on bounce).  
Note: one material can be used by multiple objects.  
Material initializer example: (source code has some more examples).
```mlog
# Orange emission, reflects all blue and half of green, has slight roughness
op add matPtr matPtr 1 # emission R
write 1 memMain matPtr 
op add matPtr matPtr 1 # emission G
write 0.5 memMain matPtr
op add matPtr matPtr 1 # emission B
write 0 memMain matPtr
op add matPtr matPtr 1 # reflection R
write 0 memMain matPtr
op add matPtr matPtr 1 # reflection G
write 0.5 memMain matPtr
op add matPtr matPtr 1 # reflection B
write 1 memMain matPtr
op add matPtr matPtr 1 # roughness
write 0.01 memMain matPtr
```

## Spheres
In #Spheres section, you can edit spheres. Make sure the variable sphereCount is equal to sphere count.  
Spheres have position (XYZ), radius (Float) and material (UInt, the index of material to use)  
Sphere initializer example:
```mlog
# Sphere at (12; 34; 56), radius is 20, material is 2
op add spherePtr spherePtr 1 # position X
write 12 memMain spherePtr
op add spherePtr spherePtr 1 # position Y
write 34 memMain spherePtr
op add spherePtr spherePtr 1 # position Z
write 56 memMain spherePtr
op add spherePtr spherePtr 1 # radius
write 20 memMain spherePtr
op add spherePtr spherePtr 1 # material
write 2 memMain spherePtr
```

## Y-planes
In #Y-planes section, you can edit y-planes. Make sure the variable yplaneCount is equal to y-plane count.  
Y-planes have y-position (Float), cell width (Float) and materials (two UInts, indexes of materials to use)  
Y-plane initializer example:
```mlog
# Y-position is -10, cell width is 20, materials are 0th and 1st
op add yplanePtr yplanePtr 1 # y
write -10 memMain yplanePtr
op add yplanePtr yplanePtr 1 # cell width
write 20 memMain yplanePtr
op add yplanePtr yplanePtr 1 # material 0
write 1 memMain yplanePtr
op add yplanePtr yplanePtr 1 # material 1
write 0 memMain yplanePtr
```

## General variables
In #General variables section, you can change other rendering parameters:
- Background color (RGB),
- Stars color (RGB; stars are explained below),
- Stars threshold (Amount),
- Stars size (Float from 0 to 360),
- Bounce count (UInt) - how many bounces a ray can do. High values are not recommended,
- Along-normal shift (Float) - length of the shift that's added to new ray's origin to prevent it from colliding the same object again, shift's direction is same as object's normal in collision point.

## About stars
A coordinate-dependent pseudo-random noise is being added to the color of the sky. This can be used to add stars or noise to it.
- Stars color is the color of the noise. Values contrast to sky color can be used for stars, while similar colors can be used for noise,
- Stars threshold is the amount of sky having the color of stars. Low numbers can be used for stars, while values around 0.5 can be used to add noise,
- Stars size is the size of one noise cell, it's considered to be in radians: 0 makes stars invisible, while ~3.14 makes cell size equal to the entire sky.

# How to edit GPU code
As long as you're not trying to change rendering algorithm, changing GPU code is unnecessary. However, if you still want to do it, changed code should be put into each GPU and procIndex should be changed to this GPU's index. You can do it manually or use some special tool (original machine was done with modified version of the game)
