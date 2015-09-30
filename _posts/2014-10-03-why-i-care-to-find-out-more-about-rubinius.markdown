---
layout: post
title: "Why I care to find out more about Rubinius"
date: 2014-10-03 13:40:46 -0600
categories: advanced-beginner programmer ruby rubinius
---

# The purpose of today's blog is to explore Rubinius. What is Rubinius?

### What is Rubinius? ###

The following is what Rubinius says about itself: An implementation of Ruby

designed for concurrency using native threads to run Ruby code on all the CPU cores.

Rubinius runs Ruby code fast with a low-pause generational garbage collector

and LLVM-based just-in-time (JIT) native machine code compiler. The Rubinius

core library and tools are built using Ruby, making a smaller, consistent

system that's easier for Ruby programmers to understand and modify. Rubinius

created and maintains RubySpec, containing more than 20,000 specifications,

to monitor compatibility with Matz's Ruby Implementation (MRI). Rubinius

provides a C-API interface for running C-extensions to ease migrating from MRI.

### ( huh?!? ) Let's unpack this description a bit: #####

My understanding of concurrency is that it basically does some task while another task
might still be working. Normally, Ruby is not so good at concurrency. Concurrency
is best for a functional language that uses recursion. Ruby sucks at recursion and I
have an example to prove it:

{% highlight ruby %}
```ruby
def factorial(number)
  if number <= 1
    return 1
  else
    return number * factorial(number - 1)
  end
end
```
{% endhighlight %}

I used IRB in my terminal to run this Ruby method and got a "stack-level-too-deep"
error at number = 8721.

Now, the same method using a functional language like Elixir:

```elixir
defmodule Factorial do
  def fact(0), do: 1
  def fact(number), do: number * fact( number - 1 )
end
```
Using IEX, which is the Elixir version of IRB, I was able to quickly find
the factorial of 20,000. I waited two minutes, but was able to go up to the
factorial of 150,000.

So, if Rubinius is concurrent, and it is written in Ruby, then there must be
some kick-ass stuff going on under the hood.

...ok, well I'm going to turn this into a multi-part because I'm getting bored
and need to work on other stuff...

###### Next up...what are threads? what is low-pause generational garbage collection? #####

###### What is a LLVM-based just-in-time (JIT) native machine code compiler? ####

###### till next time #####
