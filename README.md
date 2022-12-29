# Tolerances for 3D Printing

## Eh what?
Currently downloading a functional model for 3D printing is a bit hit-and-miss:
If the part needs a bearing to be inserted, can I be sure that it will fit or will I need to adjust the 3D model? 
This comes down to two things:
 - A lack of reproducability between machines and slicer settings
 - No way to specify how important a dimension is.

This is _exactly_ the reason why machinists invented fits and tolerances: so they could pass a set of engineering drawings
(ie the 3D model file) and be certain that the part that was manufactured would serve the intended role.

Imagine a world where you could just download a functional part and be sure that everything would just ... function!
Let's explore what it would require to make this a reality.

## Tolerances? What are they?
Currently 3D printing doesn't really have the concept of fits and tolerances. Lets's say you want a hole to put an 
M3 bolt through. The naieve approach is to ... make a 3mm hole. This will fail for several reasons. One is that
if the machine produces an _exactly_ 3mm hole, the bolt won't fit as ithe edge atoms of the bolt attempt to bond to the
edge atoms of hole. You need to leave a bit of slop to allow it to slide through. Then there is the inaccuracy of the
manufacturing process. A 3D printer is a bit inaccurate, so you may add an additional tolerance on to allow for the
layer lines and over-extrusion and hole-thermal-shrinking meaning that the 3D printer makes holes slightly smaller than
expected (although modern slicers are getting better at this).

All this means that when you want a hole to put in a 3mm bolt, chances are that you just CAD'd up a 3.2mm hole and
called it good enough. 

Now, fitting a 3mm bolt is not that hard because you don't need precision. But what about a bearing. What if you want
to press-fit a 608zz bearing? What size do you make the hole then?

Machinists have solved this issue. It's called a [fit](https://en.wikipedia.org/wiki/Engineering_fit). You want your 
608zz bearing to fit? Specify a 22mm hole with an H7 press-fit and the manufacturing company will know what you mean.
Machinists also solved other similar problems. 

 - Does it matter if this surface is slightly offset? Add [a tolerance](https://en.wikipedia.org/wiki/Engineering_tolerance).
 - How smooth should the surface be? An N12 surface roughness specifies that surface deviations of 0.05mm are OK, but
   an N3 requires the surface to have deviations of less than 0.001mm.

## The limits of 3D printing

### The perfect 3D printer
Lets for a moment assume that 3D printers are perfect. There are no layer lines, no surface imperfections. If you specify
a 20mm cube, it will produce a 20.000mm cube down to the size of a PLA molecule. We still need tolerances to tell if a hole
should be a rattling fit or a press fit: a 20mm hole for a bolt is not the same as a 20mm hole for a bearing.

For this we need to specify that a surface that is _must_ be offset in a certain direction by a variable range.
For example, a 20mm hole with a H7 press fit must be produced between 20.000 and 20.021 mm. 

### A slightly less perfect 3D printer
Now lets consider a slighly less perfect 3D printer. The surface will still be perfectly smooth and layer-line-free, but
it may be slightly dimensionally inaccurate. Maybe there is 0.1mm backlash in the leadscrews so that our 20mm cube may
come out 20.1mm on the X axis and 19.9mm on the Y axis. What if it is absolutely vital that our 20mm cube is not bigger
than 20mm. We want to shift the manufacturing variability from 19.9-20.1 so that the cube produced is 19.8-20.0mm.

Again, we are specifying that a surface that is _must_ be offset in a certain direction by a variable range.

_A quick aside for a real world change that may not be hard to accomplish: consider the Z axis and layer height.
On any 3D printer printing at 0.2mm layer height the Z axis has an implicit surface tolerance of horizontal surfaces
of ±0.1mm. In the future a slicer may be able to dynamically adjust a layer height to achieve a more perfect 
result. This is the future I want to see_ 

### A real 3D printer
A real 3D printer's most noticable artifact is ... layer lines. Layer lines are a result of trading off surface finish
(small layer lines) for speed (large layer lines). Wouldn't it be nice if we could tell the printer "I don't care about
layer lines on this surface, but on this other surface it needs to be as smooth as possible.
For example no-one can see the inside of a costume helmet, so why not print inside layers at 0.3mm, but the outside
may be painted with a highly reflective paint, so those surface should be printed with a layer height of 0.1mm.

So what exactly are we specifying here? I think we are specifying acceptable surface roughness. Or how far the surface
can deviate from it's nominal position within the bounds of it's tolerance.

## Visual summary:
![image](https://user-images.githubusercontent.com/13490050/210017918-25fa92d8-3c50-4aa7-8bf7-7b01a26d5885.png)


## What do we need to develop?
1. CAD tools. Either for annotating existing 3D models, or for adding tolerances at design time. (n parametric CAD why
do I specify tolerances on the drawing rather than on the dimension?)
2. Interchange Formats. How can we store the tolerancing information? Can we extend STL format? Currently STL only supports
triangles, but there is no reason why you couldn't add on additinal surface information at the end of the file without losing
STL compatibility.
3. Slicers. Slicers need to understand 3D printers even more (they are already amazing pieces of software) and make assumptions
about how they will produce the part. 



