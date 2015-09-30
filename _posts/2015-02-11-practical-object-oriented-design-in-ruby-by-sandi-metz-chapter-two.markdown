---
layout: post
title: "Practical Object-Oriented Design in Ruby by Sandi Metz (Chapter Two)"
date: 2015-02-11 08:34:35 -0700
categories: advanced-beginners, competent, ruby, rails
---

## Book-Review: Chapter Two (Designing Classes with a Single Responsibility)

###### DISCLAIMER/Acknowledement:

If there are any sentences or phrases that make a lot of sense to you then it was
most likely copied word for word from Sandi's book. She continues to inspire.


### Chapter Gist

>  - The path to changeable and maintainable object-oriented software begins with classes
that have a single responsibility
>  - Refactoring a class so that all of its methods have a single responsibility has a clarifying
effect on the class
>  - If a bit of code inside a method needs a comment, extract that bit into a separate
method
>  - Others will follow the pattern you have established and create small, reusable
methods in turn
>  - Small methods lower the barriers to improving your design

### Some specific steps

>  - Test your class and methods:
>    - If the simplest description you can devise uses the word "and", the class
likely has more than one responsibility
>    - If it uses the word "or," then the class has more than one responsibility and
      aren't even very related
>  - Behavior is captured in methods and invoked by sending messages so always wrap
instance variables in accessor methods instead of directly referring to variables

{% highlight ruby %}
class Gear
  def initialize(chainring, cog)
    @chainring = chainring
    @cog       = cog
  end

  def ratio
    @chainring / @cog.to_f
  end
end
{% endhighlight %}

Using `attr_reader` caused Ruby to create simple wrapper methods
for the variables

{% highlight ruby %}
class Gear
  attr_reader :chainring, :cog
  def initialize(chainring, cog)
    @chainring = chainring
    @cog       = cog
  end

  def ratio
    chainring / cog.to_f
  end
end
{% endhighlight %}
>  - Methods, like classes, should have a single responsibility

Look at the diameters method of the class below

{% highlight ruby %}

def diameters
  wheels.collect {|wheel| wheel.rim + (wheel.tire * 2)}
end
{% endhighlight %}

The method clearly has two responsibilities: it iterates over the wheels and it calculates the diameter of each wheel.
Here is a possible refactor that splits these two responsibilities into just one method a piece:

{% highlight ruby %}
def diameters
  wheels.collect {|wheel| diameter(wheel)}
end

def diameter(wheel)
  wheel.rim + (wheel.tire * 2)
end
{% endhighlight %}
