---
title: Mixing Maya UI elements into a QtDesigner interface
layout: post
permalink: blog/mixing-maya-ui-elements-into-a-qt-designer-interface
---

![pymel_qt_ui](/uploads/maya/pymel_qt_ui.png)

One of the nice things in Maya is being able to make interfaces for tool using Qt and Qt Designer. Unfortunately not everything in Qt has a corresponding UI element in maya, even for some things that you think there would be. One most glaring omissions is that a Qt float spinbox doesn't get mapped to a maya float field.

While recreating the Comet Joint Orient tool over to python I ran into this issue while trying to replicate the world up and tweak vector fields. If you just want to get the value from a float spinbox you can by routing its information into something maya does know how to talk to, usually a line edit, using Qt signals/slots mechanism. However the comet version of the tool has buttons that set the field to a given preset vector and I couldn't get away with only reading the values. The obvious solution to that is to try creating float fields in maya and add them to the Qt interface.

In order to add new elements to the interface we need to know which layout to we want to put things in. For this tool we have two layers of nested layouts in Qt Designer, horizontal layouts for each row of elements and a vertical layout that hold all the horizontal layouts. If we take the Show Axis button for example and ask it what layout its in we would expect something like JointOrient|verticalLayout|horizontalLayout1. Lets see what we get.

```python
win_name = pm.loadUI(uiFile='path/to/ui/file')
# get buttons in interface
buttons = [x for x in pm.lsUI(type='button') if win_name in x]
print next(x for x in buttons if 'show_axis' in x).getParent()
# Result
JointOrient|verticalLayout 
```
Wait a minute were is our horizontal layout? So it turns out when you have nested layouts maya decides to only see the parent layout. This obviously causes us a problem when we are trying to find a specific layout, so what do we do. 

The answer to this problem is to organize our interface using widgets instead of layouts, at least in the places where we want to add things after loading the ui file. Once we have a widget we can set it to have a horizontal layout for the things we add.

![Interface as Seen in QtDesigner](/uploads/maya/qt_designer_diagram.png)

Now with our widget layout lets try the same thing that we did with the show axis button. Make a button inside the widget to look for. We are expecting something like JointOrient|verticalLayout|widget.

```python
win_name = pm.loadUI(uiFile='path/to/ui/file')
# get buttons in interface
buttons = [x for x in pm.lsUI(type='button') if win_name in x]
print next(x for x in buttons if 'world_placeholder' in x).getParent()
# Result
JointOrient|verticalLayout|horizontalLayout_5
```

Alright so now we have a horizontalLayout but why didn't our widget show up in the path?  Whats going on is that maya doesn't see the widget, but since it sees the top layout in a widget we can still get what we want. Since we have a reference to the layout from our place holder button we can just delete the button and use the reference as the parent for the new elements we add.

```python
win_name = pm.loadUI(uiFile='path/to/ui/file')
# get buttons in interface
buttons = [x for x in pm.lsUI(type='button') if win_name in x]
 
# get location for where world vector goes and get rid of placeholder
world_placeholder = next(x for x in buttons if 'world_placeholder' in x)
world = world_placeholder.getParent()
pm.deleteUI(world_placeholder)
 
# make floatFields and buttons using normal ui commands
pm.text(label='World Up Dir:', p=world)
world_x = pm.floatField(v=1.0, p=world, pre=2)
world_y = pm.floatField(v=0.0, p=world, pre=2)
world_z = pm.floatField(v=0.0, p=world, pre=2)
pm.button(label='X', p=world, c=set_world_x)
pm.button(label='Y', p=world, c=set_world_y)
pm.button(label='Z', p=world, c=set_world_z) 
```

## links
2023/03/05 I had links to full scripts on a bitbucket repo that is now dead.
I probably still have the code somewhere but haven't had time to rehost it anywhere