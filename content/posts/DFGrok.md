---
title: "Visualising Objective-C: Introducing DFGrok!"
date: 2013-09-04
tags: [ "Objective-C" ]
---

As developers we spend a lot of time sketching out class relationships and program flow in notebooks or on whiteboards, as a shared point of reference when communicating ideas or as a personal memory aid.  A few months ago I discovered a handy online generator called [yUML](http://yuml.me).  It describes itself as being for "light, back-of-napkin style UML usage", and provides a simple syntax that allows you to quickly type out class relationships which it then renders into basic, prettified UML.  It's ideal for everyday use where formal UML is time-consuming to produce and unfriendly on the eye.

I wanted to create a tool that would allow a developer to quickly see the relationships between a limited set of classes.  A lot of automated UML tools tend to dump the whole project into an incomprehensible diagrammatic bolognese which is of no use to anyone. I also didn't want it to include too much information in the UML- if it shows everything then you really might as well be looking at the code.  So, I present [DFGrok, a command line application for auto-generating yUML from Objective-C implementation files][DFGrok].

[DFGrok][DFGrok] only maps the classes you input, and automatically pulls in direct relatives.  Importantly it ignores anything that isn't a connection between the specified classes.  It will show immediate superclasses, stong and weak ownership, and interface inheritance (but not interfaces which do not connect two key classes).  It even detects one-to-many ownership by looking at which objects get stored in member *NSArrays* and *NSDictionaries*.  A cool feature is the use of colour to represent class types to reduce clutter.  For example, you can specify that UIViewController descendants are green, both removing the need to show the UIViewController class, and allowing better visual distinction between elements.

[DFGrok][DFGrok] is not intended as a tool for producing documentation, or for digging down into details.  Its strengths are in what it *doesn't* do.  If you have an unfamiliar feature it can just help you to get a high level overview of the key relationships to be aware of.

*Note: currently only projects which have member variables exclusively as **properties** are supported!*

##### Screenshot (key below):
![Example output](/images/yuml2.png)

##### Key:
![Key](/images/yumlkey.png)

[DFGrok]: https://github.com/darkFunction/DFGrok
