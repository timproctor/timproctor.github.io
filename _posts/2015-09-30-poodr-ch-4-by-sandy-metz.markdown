---
layout: post
title: "POODR by Sandi Metz Chapter Three"
date: 2015-09-30 12:57:12 -0600
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

In the example below, Customer knows that: 1. He wants a list of trips. 2. There's an object that implements the *suitable_trips* message. 3. Part of finding a suitable trip means finding a *suitable_bicycle* message.

![uml-#2]({{ site.url }}/assets/images/chapter4_2.png)

The problem is that **Customer** not only knows *what* he wants, he also knows *how* other objects should collaborate to provide it. The **Customer** class has become the owner of the application rules that assess trip suitability.

#### Asking for "What" Instead of Telling "How" ####

The distinction between a message that asks for what the sender wants and a message that tells the receiver how to behave may seem subtle but the consequences are significant. Understanding the difference is a key part of creating reusable classes with will-defined public interfaces.

We will use a new use case to illustrate the "what" versus "how": A trip, in order to start, needs to ensure that all its bicycles are mechanically sound. **Trip** could know exactly how to make a bike ready for a trip and could ask a **Mechanic** to do each of those things as illustrated in the next example.

![uml-#3]({{ site.url }}/assets/images/chapter4_3.png)

A *Trip* tells a *Mechanic* how to prepare a *Bicycle*, almost as if *Trip* were the main program and *Mechanic* a bunch of callable functions. In this design, *Trip* is the only object that knows exactly how to prepare a bike; getting a bike prepared requires using a *Trip* or duplicating the code. *Trip's* context is large, as is *Mechanic's* public interface. These two classes are not islands with bridges between them, they are instead a single, woven cloth.

Here is an alternative where *Trip* asks *Mechanic* to prepare each *Bicyle*, leaving the implementation details to *Mechanic*.

![uml-#4]({{ site.url }}/assets/images/chapter4_4.png)

This example is more object-oriented. Here, a *Trip* asks a *Mechanic* to prepare a *Bicycle*. *Trip's* context is reduced, and *Mechanic's* public interface is smaller. Additionally, *Mechanic's* public interface is now something that any object may profitably use; you don't need a *Trip* to prepare a bike. These objects now communicate in a few well-defined ways; they are less coupled and more easily reusable.

The next example illustrates a third alternative sequence diagram for *Trip* preparation. *Trip* tells *Mechanic* what it wants, that is, to be prepared, and passes itself along as an argument.

![uml-#5]({{ site.url }}/assets/images/chapter4_5.png)

In this example, *Trip* doesn't know or care that it has a *Mechanic* and it doesn't have any idea what the *Mechanic* will do. *Trip* merely holds onto an object which it will send *prepare_trip*; it trusts the receiver of this message to behave appropriately.
