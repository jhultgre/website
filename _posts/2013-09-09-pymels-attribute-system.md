---
title: Pymel's attribute system
layout: post
permalink: blog/pymels-attribute-system
---
So last time we looked at making some utility nodes that we can use in our scripts. Today we'll look at how to actually use them and in doing so we'll look into how pymel handles attributes.

If you're coming from mel or maya.cmds the syntax you're used to to access attributes will look this.
```python
# mel
getAttr cube.translateX;
# maya.cmds
cmds.getAttr('cube.translateX') 
```
In pymel we can get an attribute in the same way as maya.cmds.
```python
pm.getAttr('cube.translateX') 
```
However due to pymel representing everything as a PyNode object we have a few other ways to get attributes.
```python
# .attr method
mycube = pm.polyCube()[0]
mycube.attr('translateX')
 
# short hand method
mycube.translateX
mycube.tx
 
# node constructor method
pm.PyNode('cube.translateX')
pm.Attribute('cube.translateX') 
```
The  .attr method looks pretty similar  to getAttr except that you call it as a method on a PyNode. The common reason to use this is when you don't know what attribute you want when writing your code. You might be getting which attribute you want at runtime from either an interface or a function.

Using the short hand syntax is convenient when know what attribute you want from the start. You can use either short names or long names of any attribute including attributes you have added yourself. I usually use this way because it makes my code easier to type and read.

The third way of getting an attribute is using node constructors. I've listed two ways to use method pm.PyNode and pm.Attribute. Both of these return the same thing an attribute object. In fact the .attr and short hand syntax also return attribute objects. 

Having these attribute objects lets us easily keep track of the attribute we want no matter what else if going on in our scene. We can rename or change the parent of the object it is attached too and the attribute object will still point to the correct place. 

In order to use our attribute there are a few methods to know.
```python
myattr = mycube.translateX
 
# get and set values
myattr.get()
myattr.set(4)
 
# set and break connections
myattr.connect(mysphere.translateY)
myattr.disconnect(mysphere.translateY)
 
# short hand for connections
myattr >> mysphere.translateY
myattr // mysphere.translateY 
```
Now .get and .set should be pretty self explanatory, they let you find out or change the value of the attribute.

If you want to change connections to an attribute you have a choice in syntax either long form or short form. For making connections I generally use the short form because its faster to type, but if you don't do a lot of scripting you may want to use the long from so you remember what you are doing.

When disconnecting connections I always use the long from. This is a little bit preference for me because I like to be more verbose when I'm getting rid of something and also because the .disconnect methods has a few options that the short hand syntax doesn't.
```
# disconnect a certain connection
myattr.disconnect(mysphere.translateY)
 
# disconnect all connections
myattr.disconnect()
 
# disconnect all inputs 
myattr.disconnect(inputs=True)
 
# disconnect all outputs
myattr.disconnect(outputs=True) 
```
If you want more information on attributes check out the article in the pymel docs. 
http://download.autodesk.com/global/docs/maya2014/ja_jp/PyMel/attributes.html

If you want to see an example of using attributes you can look at a simple script I made to setup a blend attribute for a selection of constraints.

2023/3/5: there was link to a bitbucket repo that no longer exists. I still have the code somewhere, but haven't had time to rehost it anywhere