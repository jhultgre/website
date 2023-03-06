---
title: Mayapy tips
layout: post
permalink: blog/mayapy-tips
---
I've  been  working at 2K as a technical artist for the last 4 months and I have been using mayapy a lot so I thought I'd list a few tips that have saved me a lot of time or were tricky to figure out.

Make a mayapy build system in Sublime Text. Hitting ctrl+b is a lot faster then  going to the cmd line to launch your program.
If you're using MayaMaya[https://github.com/danbradham/manymaya] write a function that just takes a tuple and unpacks it for your main function. It makes it easier to go back and forth between maya and mayapy and you can catch and log errors in this function.

```python
@instance
def batch_args(args):
    actual_function(*args)  # unpack the args to the function

@instance
def batch_kwargs(kwargs):
    actual_function(**kwargs)  # unpack the keyword arguments to the function
```
If you want to use a normal python subprocess for something, you need to pass subprocess.Popen  the environment variables you want it to use. Specifically `os.environ['PYTHONHOME']` needs to get set back to your python's install folder.