---
layout: post
title: Practical Object-Oriented Design in Ruby by Sandi Metz
date: 2015-01-19 15:23:25 -0700
categories: ruby rails book-review poodr
---

## Book-Review: Chapter One (Object-Oriented Design) ##

###### DISCLAIMER/Acknowledement:

If there are any sentences or phrases that make a lot of sense to you then it was
most likely copied word for word from Sandi's book. She continues to inspire.

### Chapter One (Object-Oriented Design)

What is an object-oriented language? What is design? These two questions are answered
in this first chapter.

#### Object-Oriented

Ruby is an example of an object-oriented language compared to a functional language
like Elixir.

###### Elixer has other characteristics of a functional language like:

>   - knows about a small, fixed set of different kinds of data, things like strings, numbers,
arrays, files -- called *data types*.

>   - every possible data type and operation already exists; these things are build into
the syntax of the language

>   - data is one thing, behavior is something completely different

###### Ruby has the following object-oriented characteristics:

>   - instead of dividing data and behavior into two separate spheres, Ruby combines them
together into a single thing, an *object*

>   - objects invoke one another's behavior by sending each other *messages*

>   - it's up to Ruby to find and invoke the right method of the correct object for any
sent message

>   - it's far more common to desire to manufacture a bunch of objects that have identical
behavior but encapsulate different data

>   - every object decides for itself how much, or how little, of its data to expose

>   - every newly instantiated class *implements* the same methods and uses the same
attribute names but each contains its own personal data

>   - you can invent brand new types of pre-predefined operations on your own

#### design

###### What are the tools of design?

>   - as a sculptor has chisels and files, an object-oriented designer has *principles*
and *patterns*

###### some *principles* are: ######

>   - SOLID: **S**ingle Responsibility, **O**pen-Closed, **L**iskov Substitution, **I**nterface Segregation, and
**D**ependency Inversion

>   - DRY: **D**on't **R**epeat **Y**ourself

>   - LoD: **L**aw **o**f **D**emeter

###### some *patterns* are:  ######

>   - 1994 - (Gof), Erich Gamma, Richard Helm, Ralph Johnson, Jon Vlissides wrote *Design Patterns*

>   - *Design Patterns* gave an entire generation of programmers the means to communicate
and collaborate

>   - the popularity of patterns led to a kind of pattern abuse by novice programmers, who, in an
excess of well-meaning zeal, applied perfectly good patterns to the wrong problems

###### Why isn't design easy?

>   - If we know to use the tools of design, *patterns* and *principles*, then why isn't
design easy? If you think of software as custom furniture, then principles and patterns
are like woodworking tools. Knowing how software should look when it's done does
not cause it to build itself; applications come into existence because some programmer
applied the tools.

###### Symptoms of poor design:

>   - successful but *undesigned* applications carry the seeds of their own destruction;
they are easy to write but gradually become impossible to change

>   - the early promise of painless development gradually fails and optimism turns to despair
as programmers begin to greet every change request with "Yes, I can add that feature,
*but it will break everything*"

>   - more advanced programmers are aware of OO design techniques but do not yet undersand
how to apply them, these programmers fall into the trap of overdesign

###### The processes of creating and evaluating design:

creating design with Agile:

>   - customers can't define the software they want before seeing it, so it's best to show
them sooner rather than later

>   - you should build software in tiny increments, gradually iterating your way into an
application that meets the customer's true need

>   - the most cost-effective way to produce what customers really want is to collaborate
with them

>   - there is absolutely no point in doing a Big Up Front Design (because it cannot possibly
be correct), and second, no one can predict when the application will be done
(because you don't know in advance what it will eventually do)

>   - any big design created in advance of working software cannot be correct, to write
the application as specified guarantees that it will not meet the customer's needs

>   - when a project misses its delivery deadline, even if this happened because of changes
to the specification, the programmers are at fault, however; it is delivered on time
but doesn't fulfill the actual need, the specification must have been wrong, so
the customer gets the blame

>   - Agile acknowledges that certainty is unattainable *in advance* of the application's
existence; Agile's acceptance of this truth allows it to provide strategies to
overcome the handicap of developing software while knowing neither the target nor
the timeline

>   - Agile processes *guarentee change* and your ability to make these changes depends
on your application's design

evaluating design with Agile:

>   - there are numerous Ruby gems (a google search on *ruby metrics* will turn up the most recent)
that assess how well your code follows OOD principles

>   - bad OOD metrics are indisputably a sign of bad design; code that scores poorly *will*
be hard to change, good scores don't prove the opposite, they don't guarantee that the
next change you make will be easy or cheap

>   - OOD metrics cannot identify designs that do the wrong thing in the right way

>   - if lack of a feature will force you out of business today it doesn't matter how much
it will cost to deal with the code tomorrow; making a design compromise borrows time
from the future, known as taking on *technical debt*.

>   - if design takes half your time this month and does not start returning dividends
for a year, it may not be worth it

>   - if design takes half of your time this morning, pays that time back this afternoon,
and then continues to provide benefits for the life of the app, you get a kind of
daily compounding interest on your time; this design effort pays off forever

>   - inexperienced programmers who do a lot of anticipatory design may never reach a point
where their earlier design efforts pay off, skilled designers who write carefully crafted
code this morning may save money this afternoon, your experience likely lies somewhere
between these extremes, the remainder of Sandi's book teaches skills you can use
to shift the break-even point in your favor
