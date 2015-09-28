---
layout: post
title: "more on Rubinius"
date: 2014-10-10 16:32:16 -0600
comments: true
categories: ruby rubinius
---
### ...what are threads?

Rubinius processes threads of memory on both cpu-cores in parallel. Evan explained
to me that running concurrent and in parallel are two different things. Imagine
that you have a math test and a biology test side by side. You take a pencil
and answer math question \#1 then biology question \#1 and so on until both tests are complete.
This is an example of running concurrent. Now, imagine that you have the ability
to hold a pencil in each hand and you answer math question \#1 and biology question \#1
at the SAME time. This is an example of running parallel. According to Evan Phoenix,
the creator of Rubinius - it's both parrallel and concurrent.

### what is low-pause generational garbage collection?

Garbage collection was one of the first features of Rubinius that Evan Phoenix
and his team developed back in 2006. Evan was influenced by the "immix" paper
on garbage collection. Using low-pause generational garbage collection works
for implementations that want to run concurrently.

### What is a LLVM-based just-in-time (JIT) native machine code compiler?

LLVM used to stand for low-level virtual machine back in 2004, when it was an
open source project based out of the University of Illinois. LLVM is now the name
of a whole umbrella of projects. LLVM no longer stands for anything and for the
Rubinius project, Ruby is compiled down to C++.

### RubySpec and Brian Shirai (Ford)

It would be impossible to talk about Rubinius without mentioning Brian Shirai
(Ford). He has been the steady work horse on the project since at least 2007. Brian
loves Ruby. He started the RubySpec project and is passionate about
the need for a Ruby Design Process. He envisions that this Design Process would
be run by a committee of Ruby stakeholders and would follow a step-wise process for
Ruby design. Brian would like Rubinius to replace any desire for Ruby programmers
to leave Ruby for frameworks like Node.js. He posted once on Twitter that he could
take any Node.js app and build it to be just as faster or faster in Rubinius.

I'm going to leave Rubinius for now...
