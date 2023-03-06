---
title: Using utility shading nodes in Pymel
layout: post
permalink: blog/using-utility-shading-nodes-in-pymel
---
When building a rig utility nodes like mulipyDivide or reverse are a handy way of setting up functionality of controls. But after you've setup an IKFK blend for what feels like the hundredth time you start looking for a way to automate connecting up all those attributes. 

If you go to the pymel docs and search for how to create these nodes you probably wont find what you where hoping for. While the latest docs have information on the nodes themselves there aren't any good examples like there are for most of the  other pymel commands.  

Since the docs aren't helping lets go back to Maya and look at what is output to the script editor when we make these nodes by hand. Open up the hypershade or node editor and make some of the nodes we want. You should see something like this.
```mel
shadingNode -asUtility reverse;
shadingNode -asUtility multiplyDivide;
shadingNode -asUtility vectorProduct; 
```
Looking at these they are all made with the shadingNode command. If we go back to the pymel docs we can find a command that matches up in rendering/shadingNode. The examples are still pretty sparse but essentially we use the command like this.
```python
import pymel.core as pm
pm.shadingNode('[NODETYPE]', [NODECLASSIFICATION]=True) 
```
For [NODETYPE] we enter what node we want as a string, in our case 'reverse' or 'multiplyDivide'. [NODECLASSIFICATION] is needed to tell maya where to put this node in the hypershade. We use as asUtility=True since we are making utility nodes, if you are making a light or shader you should use asLight or asShader respectively following the docs. So when we fill those things in our code will look like this.
```python
utility = pm.shadingNode('reverse', asUtility=True)
```  
The command returns a pynode which contains the node made which we can store in a variable in this case utility. With our node safely in a variable we can use it however we want.

Now that we have our utility nodes how do we hook it up? 

To do this we have to look into pymel's attribute system which I'll be getting into next time.