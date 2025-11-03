# Introduction
Introduction

**ZwyLib** is a compact plugin-library that originally started as part of various plugins from the developer’s channel , and is now available to anyone who might find it useful.

Getting Started[](#getting-started)
-----------------------------------

Any plugin that wants to use ZwyLib’s tools must first import it (after installing it via this post ):

```
# __id__, __name__, ...
 
try:
    import zwylib  # import the library
except (ImportError, ModuleNotFoundError):
    # zwylib not found — its tools cannot be used. raise an error
    raise Exception("Cannot run without ZwyLib. Please install it.")
 
class MyPlugin(BasePlugin):
    ...  # your plugin logic
```
