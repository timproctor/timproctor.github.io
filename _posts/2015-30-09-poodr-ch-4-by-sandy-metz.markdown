---
layout: post
title: "POODR by Sandi Metz Chapter Three"
date: 2015-30-09 12:57:12 -0600
categories: ruby rails
---

## Book-Review: Chapter Four (Creating Flexible Interfaces)

###### DISCLAIMER/Acknowledement:

If there are any sentences or phrases that make a lot of sense to you then it was
most likely copied word for word from Sandi's book. She continues to inspire.

### Finding the Public Interface

The new example for this chapter is a Bicycle Touring Company and the proposed use case is: A customer, in order to choose a trip, would like to see a list of available trips of appropriate difficulty, on a specific date, where rental bicycles are available.

The word **interface** is used to refer to the kind of interface that is within a class. Classes implement methods, some of those methods are intended to be used by others and these methods make up its public interface. You probably expect to have Customer, Trip, Route, Bike and Mechanic classes. These classes spring to mind because they represent *nouns* in the application that have both *data* and *behaviour*. These are called **domain objects**. Using an UML (Unified Modeling Language) sequence as a new tool is introduced in this chapter. In the example below each object is represented by a box. It contains two objects, the **Customer** and the class **Trip**. Messages are shown as horizontal lines. When a message is sent, the line is labeled with the message name. Message lines end or begin with an arrow; this arrow points to the receiver. When an object is busy processing a received message, it is *active* and its vertical line is expanded to a vertical rectangle.

![uml-#1]({{ site.url}}/assets/images/chapter4_1.png)

This transition from class-based design to message-based design is a turning point in your design career. The message-based perspective yields more flexible applications than does the class-based perspective. Changing the fundamental design question from "I know I need this class, what should it do?" to "I need to send this message, who should respond to it?" is the first step in that direction. In the above example it is reasonable for **Customer** to send a *suitable_trips* message but **Trip** probably should not be receiving the message.
