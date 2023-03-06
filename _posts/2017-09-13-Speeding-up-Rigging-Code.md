---
title: Speeding up Rigging Code
layout: post
permalink: blog/speeding-up-rigging-code
---

So our game NBA 2K18 comes out Friday  so the art team has been pretty  relaxed for the past couple of weeks. With this down time I've finally had a moment to handle our largest complaint from the character team, Speed.

The main complaints about speed were that  our rigging  code took too long and  our tools felt unresponsive .

I started by  running cProfile on our rigging code to  find any bottlenecks.  The starting profile showed that it was talking about 96 seconds to rig my test character.  It also showed that we were spending a lot of time  doing file I/O and making blendshapes. 

I noticed that we were reading in a bunch of files,  moving them all by some amount, then saving them out to use later.  I saved about 10 seconds by saving  out the transform values in a text file then applying it right before we need to use each object in the rig.  There was some cleanup  code that was run on each of those intermediate files that  need to be modified to run at import time  instead of  on a  clean file, but that was fairly straight forward.

We make tons of blendshapes by wrap deforming  things to the main geo. I saved 5 seconds by  not making blendshapes  were we didn't need to. So no right side shapes on meshes that are only affected by the left side.

I tried checking about 10 percent of the  verts on the meshes to see if anything moved before making a blendshape. Unfortunately  vert look up is pretty slow in pymel so it didn't really save any time.

I also switched from duplicating the wrapped mesh and adding all the blendshapes at once to repeatedly adding one blendshape from the wrapped mesh to the final mesh, breaking the connections between them, and  renaming the alias by hand.  It didn't save  much time either but I felt more confident that the blend shape names wouldn't be renamed by maya.

I also wrote my own code to import objs for blend shapes. Once I have one of the blendshapes loaded I duplicate it  then I only need to read the vert positions of every other shape and set the point on the duplicate. It was just barely faster  but I think I might be able to  improve it with multithreading. 

We also have some generic blendshapes that most characters use.  I have a modified version of the blendshape import code that calculates the character specific versions of those files using numpy arrays.  Using arrays of vert positions you can get  the final blend shape's vert positions  with  code like this. We calculate the characters delta first so we can reuse it for all the generic shapes.

```python
character_shape_delta = character_base_shape - generic_baseshape
for generic_pose in generic_poses:
    generated_blendshape = characer_shape_delta + generic_pose

```
Finally I slimmed down some of the partial rigs we use to generate things, saving another 10 seconds or so there.

In the end I ended up saving 30 second with my best test time of 66 seconds. I was hoping to get under a minute, but I hit a brick wall there and I was ready to move onto speeding up our tools which I will talk a little bit about  later.

## Final thoughts:
Most of  our rigging code goes back at least 4 years and when you are releasing a yearly game a lot of things get added and removed from it  every time, leaving a lot of cruf sitting around.  I think every time I tried to answer a "TODO: why are we doing things this way" I speed things up a little bit, or at least I could provide an actual answer to the question.