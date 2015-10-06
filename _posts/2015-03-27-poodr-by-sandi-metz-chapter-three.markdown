---
layout: post
title: "POODR by Sandi Metz Chapter Three"
date: 2015-03-27 13:39:12 -0600
categories: ruby rails book-review poodr
---

## Book-Review: Chapter Three (Managing Dependencies)

###### DISCLAIMER/Acknowledement:

If there are any sentences or phrases that make a lot of sense to you then it was
most likely copied word for word from Sandi's book. She continues to inspire.

### Understanding Dependencies ###

Here is a couple of classes that will get refactored with each lesson:

{% highlight ruby %}
class Gear
  attr_reader :chainring, :cog, :rim, :tire
  def initialize(chainring, cog, rim, tire)
    @chainring = chainring
    @cog       = cog
    @rim       = rim
    @tire      = tire
  end

  def gear_inches
    ratio * Wheel.new(rim, tire).diameter
  end

  def ratio
    chainring / cog.to_f
  end
# ...  
end

class Wheel
  attr_reader :rim, :tire
  def initialize(rim, tire)
    @rim  = rim
    @tire = tire
  end

  def diameter
    rim + (tire * 2)
  end
# ...  
end

Gear.new(52, 11, 26, 1.5).gear_inches
{% endhighlight %}

#### Recognizing Dependencies

>  - The name of another class. <span class="other-font">Gear</span> expects a class named <span class="other-font">Wheel</span> to exist.
>  - The name of a message that it intends to send to someone other than <span class="other-font">self</span>. <span class="other-font">Gear</span> expects a <span class="other-font">Wheel</span> instance to respond to diameter.
>  - The arguments that a message requires. <span class="other-font">Gear</span> knows that <span class="other-font">Wheel.new</span> requires a <span class="other-font">rim</span> and a <span class="other-font">Tire</span>.
>  - The order of those arguments. <span class="other-font">Gear</span> knows the first argument to <span class="other-font">Wheel.new</span> should be <span class="other-font">rim</span>, the second, <span class="other-font">Tire</span>.

Instead of being glued to <span class="other-font">Wheel</span>, this next version of <span class="other-font">Gear</span> expects to be initialized with an object that can respond to diameter:

{% highlight ruby %}
class Gear
  attr_reader :chainring, :cog, :wheel
  def initialize(chainring, cog, wheel)
    @chainring = chainring
    @cog       = cog
    @wheel     = wheel
  end

  def gear_inches
    ratio * wheel.diameter
  end
# . . .
end

Gear.new(52, 11, Wheel.new(26, 1.5)).gear_inches  
{% endhighlight %}
In the above, <span class="other-font">Gear</span> does not know about Wheel.new and this is called **dependency injection**. <span class="other-font">Gear</span> previously had explicit dependencies on the <span class="other-font">Wheel</span> class and on the type and order of its initialization arguments, but through injection these dependencies have been reduced to a single dependency on the *diameter* method. <span class="other-font">Gear</span> is now smarter because it knows less.

Isolate the creation of a new <span class="other-font">Wheel</span> inside the <span class="other-font">Gear</span> class. The intent is to explicitly expose the dependency while reducing its reach into your class. The example below moves the new instance of <span class="other-font">Wheel</span> from the <span class="other-font">Gear's</span> *gear_inches* method to <span class="other-font">Gear's</span> initialization method. Notice that this technique unconditionally creates a new <span class="other-font">Wheel</span> each time a new <span class="other-font">Gear</span> is created.

{% highlight ruby %}
class Gear
  attr_reader :chainring, :cog, :rim, :tire
  def initialize(chainring, cog, rim, tire)
    @chainring = chainring
    @cog       = cog
    @wheel     = Wheel.new(rim, tire)
  end

  def gear_inches
    ratio * wheel.diameter
  end
# . . .  
{% endhighlight %}

In the next example, creation of a new instance of <span class="other-font">Wheel</span> is deferred until *gear_inches* invokes the new <span class="other-font">wheel</span> method.

{% highlight ruby %}
class Gear
  attr_reader :chainring, :cog, :rim, :tire
  def initialize(chainring, cog, rim, tire)
    @chainring = chainring
    @cog       = cog
    @rim       = rim
    @tire      = tire
  end

  def gear_inches
    ratio * wheel.diameter
  end

  def wheel
    @wheel ||= Wheel.new(rim, tire)
  end
# . . .
{% endhighlight %}

E<span class="other-font">en tho</span>u*gh <span class="other-font">Gear</span> still knows too much because of its <span class="other-font">rim</span> and <span class="other-font">Tire</span> initialization arguments, an improvement is made by publicly exposing <span class="other-font">Gear's</span> dependency on <span class="other-font">Wheel</span>. This change makes the code more agile.

Now that you've isolated references to external class names it's time to turn your attention to external *messages*, that is, messages that are sent to someone other than self. For example, the *gear_inches* method below sends *ratio* and <span class="other-font">wheel</span> to self, but sends *diameter* to wheel:

{% highlight ruby %}
def gear_inches
    ratio * wheel.diameter
end
{% endhighlight %}
Imagine that calculating *gear_inches* required far more math and that the method looked something like this:

{% highlight ruby %}
def gear_inches
  #... a few lines of scary math
  foo = some_intermediate_result * wheel.diameter
  #... more lines of scary math
end
{% endhighlight %}

The above method now depends on <span class="other-font">Gear</span> responding to <span class="other-font">wheel</span> and on <span class="other-font">wheel</span> responding to *diameter*. You can reduce your chance of being forced to make a change to *gear_inches* by removing the external dependency and encapsulating it in a method of its own, as in the example below:

{% highlight ruby %}
def gear_inches
  #... a few lines of scary math
  foo = some_intermediate_result * diameter
  #... more lines of scary math
end

def diameter
  wheel.diameter
end
{% endhighlight %}

#### Remove Argument-Order Dependencies ###

In the example below, <span class="other-font">Gear's</span> *initialize* method takes three arguments: *chainring*, *cog*, and <span class="other-font">wheel</span>. It provides no defaults; each of these arguments is required. When a new instance of <span class="other-font">Gear</span> is created, the three arguments must be passed and they must be passed in the exact order.

{% highlight ruby %}
class Gear
  attr_reader :chainring, :cog, :wheel
  def initialize(chainring, cog, wheel)
    @chainring = chainring
    @cog       = cog
    @wheel     = wheel
  end
#  ...
end

Gear.new(52, 11, Wheel.new(26, 1.5)).gear_inches
{% endhighlight %}

Here is a better solution to argument-ordered dependencies:

{% highlight ruby %}
class Gear
  attr_reader :chainring, :cog, :wheel
  def initialize(args)
    @chainring = args[:chainring]
    @cog       = args[:cog]
    @wheel     = args[:wheel]
  end
#  ...
end

Gear.new(chainring: 52, cog: 11, wheel: Wheel.new(26, 1.5)).gear_inches
{% endhighlight %}

The above example removes every dependency on argument order. <span class="other-font">Gear</span> is now free to add or remove initialization arguments and defaults, and any change will have zero side effects in other code.

To add defaults, the example below shows how to add non-boolean defaults and boolean defaults using the `` || `` method:

{% highlight rub<span class="other-font"> %}
de</span>f* initialize(args)
  # non-boolean defaults
  @chainring = ar<span class="other-font">s[:chainrin</span>] || 40
  @cog       = args[:cog]       || 18
  @wheel     = args[:wheel]
  # boolean default
  @bool      = args[:boolean_thing] || true  
end
{% endhighlight %}

In the example below, setting the defaults in this way means that callers can actually cause *@chainring* to get set to *false* or *nil*, something that is not possible when using the `` || ``      technique.

{% highlight ruby %}
# specifying defaults using fetch
def initialize(args)
  @chainring = args.fetch(:chainring, 40)
  @cog       = args.fetch(:cog, 18)
  @wheel     = args[:wheel]
end
{% endhighlight %}

You can also completely remove the defaults from *initialize* and isolate them inside of a separate wrapping method. The *defaults* method below defines a second hash that is merged into the options hash during initialization. In this case, *merge* has the same effect as *fetch*; the defaults will get merged only if their keys are not in the hash.

{% highlight ruby %}
# specifying defaults by merging a defaults hash
def initialize(args)
  args = defaults.merge(args)
  @chainring = args[:chainring]
#  ...
end

def defaults
  { chainring: 40, cog: 18 }
end
{% endhighlight %}
This isolation technique above is useful when defaults are more complicated than numbers or strings.

Imagine next that <span class="other-font">Gear</span> is part of a framework and that its initialization method requires fixed-order arguments. Imagine also that your code has many places where you must create a new instance of <span class="other-font">Gear</span>. <span class="other-font">Gear's</span> *initailize* method is *external* to your application; it is part of an external interface over which you have no control. As the example below shows, <span class="other-font">GearWrapper</span> allows your application to create a new instance of <span class="other-font">Gear</span> using an options hash.

{% highlight ruby %}
module SomeFramework
  class Gear
    attr_reader :chainring, :cog, :wheel
    def initialize(chainring, cog, wheel)
      @chainring = chainring
      @cog       = cog
      @wheel     = wheel
    end
# . . .
  end
end

# wrap the interface to protect yourself from changes
module GearWrapper
  def self.gear(args)
    SomeFramework::Gear.new(args[:chainring],
                            args[:cog],
                            args[:wheel])
  end
end

# Now you can create a new Gear using an arguments hash.
GearWrapper.gear(chainring: 52, cog: 11, wheel: Wheel.new(26, 1.5)).gear_inches
{% endhighlight %}

#### Managing Dependency Direction ###

In the first example <span class="other-font">Gear</span> depended on <span class="other-font">Wheel</span> and now in the next example below <span class="other-font">Wheel</span> depends on <span class="other-font">Gear</span> or *ratio*. The next lesson examines how choosing dependency direction can mean the difference between a pleasing code-base and one that is harder and harder to change.

{% highlight ruby %}
class Gear
  attr_reader :chainring, :cog
  def initailize(chainring, cog)
    @chainring = chainring
    @cog       = cog
  end

  def gear_inches(diameter)
    raito * diameter
  end

  def ratio
    chainring / cog.to_f
  end

# . . .
end

class Wheel
  attr_reader :rim, :tire, :gear
  def initialize(rim, tire, chainring, cog)
    @rim  = rim
    @tire = tire
    @gear = Gear.new(chainring, cog)
  end

  def diameter
    rim + (tire * 2)
  end

  def gear_inches
    gear.gear_inches(diameter)
  end
# . . .
end

Wheel.new(26, 1.5, 52, 11).gear_inches
{% endhighlight %}

##### How to choose Dependency Direction

Pretend for a moment that your classes are people. If you were to give them advice about how to behave you would tell them to *depend on things that change less often than you do.* This is based on the following simple truths about code:

>  - Some classes are more likely than others to have changes in requirements.
>  - Concrete classes are more likely to change than abstract classes.
>  - Changing a class that has many dependents will result in widespread consequences.

An example of an abstract class is when you inject <span class="other-font">Wheel</span> into <span class="other-font">Gear</span> such that <span class="other-font">Gear</span> then depends on a *Duck* who responds to *diameter*, you are, however casually, defining an interface. This interface is an abstraction of the idea that a certain category of things will have a diameter. The abstraction was harvested from a concrete class; and depending on the abstraction is always safer than depending on a concretion because by its very nature, the abstraction is more stable.

#### Summary ###

Dependency management is core to creating future-proof applications. Injecting dependencies creates loosely coupled objects that can be reused in novel ways. Isolating dependencies allows objects to quickly adapt to unexpected changes. Depending on abstractions decreases the likelihood of facing these changes.

The key to managing dependencies is to control their direction.
