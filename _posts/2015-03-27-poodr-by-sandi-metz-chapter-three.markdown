---
layout: post
title: "POODR by Sandi Metz Chapter Three"
date: 2015-03-27 13:39:12 -0600
categories:
---

## Book-Review: Chapter Three (Managing Dependencies)

###### DISCLAIMER/Acknowledement:

If there are any sentences or phrases that make a lot of sense to you then it was
most likely copied word for word from Sandi's book. She continues to inspire.

Here is a couple of classes that will get refactored with each lesson:

```ruby
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
```

#### Recognizing Dependencies

>  - The name of another class. *Gear* expects a class named *Wheel* to exist.
>  - The name of a message that it intends to send to someone other than *self*. *Gear* expects a *Wheel* instance to respond to diameter.
>  - The arguments that a message requires. *Gear* knows that *Wheel.new* requires a *rim* and a *tire*.
>  - The order of those arguments. *Gear* knows the first argument to *Wheel.new* should be *rim*, the second, *tire*.

Instead of being glued to *Wheel*, this next version of *Gear* expects to be initialized with an object that can respond to diameter:

```ruby
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
```
In the above, *Gear* does not know about Wheel.new and this is called **dependency injection**. *Gear* previously had explicit
dependencies on the *Wheel* class and on the type and order of its initialization arguments, but through injection these dependencies have been reduced to a single dependency on the *diameter* method. *Gear* is now smarter because it knows less.

Isolate the creation of a new *Wheel* inside the *Gear* class. The intent is to explicitly expose the dependency while reducing its reach into your class. The example below moves the new instance of *Wheel* from the *Gear's gear_inches* method to *Gear's* initialization method. Notice that this technique unconditionally creates a new *Wheel* each time a new *Gear* is created.

```ruby
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
```

In the next example, creation of a new instance of *Wheel* is deferred until *gear_inches* invokes the new *wheel* method.

```ruby
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
```

Even though *Gear* still knows too much because of its *rim* and *tire* initialization arguments, an improvement is made by publicly exposing *Gear's* dependency on *Wheel*. This change makes the code more agile.

Now that you've isolated references to external class names it's time to turn your attention to external *messages*, that is, messages that are sent to someone other than self. For example, the *gear_inches* method below sends *ratio* and *wheel* to self, but sends *diameter* to wheel:

```ruby
def gear_inches
    ratio * wheel.diameter
end
```
Imagine that calculating *gear_inches* required far more math and that the method looked something like this:

```ruby
def gear_inches
  #... a few lines of scary math
  foo = some_intermediate_result * wheel.diameter
  #... more lines of scary math
end
```

The above method now depends on *Gear* responding to *wheel* and on *wheel* responding to *diameter*. You can reduce your chance of being forced to make a change to *gear_inches* by removing the external dependency and encapsulating it in a method of its own, as in the example below:

```ruby
def gear_inches
  #... a few lines of scary math
  foo = some_intermediate_result * diameter
  #... more lines of scary math
end

def diameter
  wheel.diameter
end
```

#### Remove Argument-Order Dependencies ###

In the example below, *Gear's initialize* method takes three arguments: *chainring*, *cog*, and *wheel*. It provides no defaults; each of these arguments is required. When a new instance of *Gear* is created, the three arguments must be passed and they must be passed in the exact order.

```ruby
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
```

Here is a better solution to argument-ordered dependencies:

```ruby
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
```

The above example removes every dependency on argument order. *Gear* is now free to add or remove initialization arguments and defaults, and any change will have zero side effects in other code.

To add defaults, the example below shows how to add non-boolean defaults and boolean defaults using the || method:

```ruby
def initialize(args)
  # non-boolean defaults
  @chainring = args[:chainring] || 40
  @cog       = args[:cog]       || 18
  @wheel     = args[:wheel]
  # boolean default
  @bool      = args[:boolean_thing] || true  
end
```

In the example below, setting the defaults in this way means that callers can actually cause *@chainring* to get set to *false* or *nil*, something that is not possible when using the || technique.

```ruby
# specifying defaults using fetch
def initialize(args)
  @chainring = args.fetch(:chainring, 40)
  @cog       = args.fetch(:cog, 18)
  @wheel     = args[:wheel]
end
```

You can also completely remove the defaults from *initialize* and isolate them inside of a separate wrapping method. The *defaults* method below defines a second hash that is merged into the options hash during initialization. In this case, *merge* has the same effect as *fetch*; the defaults will get merged only if their keys are not in the hash.

```ruby
# specifying defaults by merging a defaults hash
def initialize(args)
  args = defaults.merge(args)
  @chainring = args[:chainring]
#  ...
end

def defaults
  { chainring: 40, cog: 18 }
end
```
This isolation technique above is useful when defaults are more complicated than numbers or strings.

Imagine next that *Gear* is part of a framework and that its initialization method requires fixed-order arguments. Imagine also that your code has many places where you must create a new instance of *Gear*. *Gear's initailize* method is *external* to your application; it is part of an external interface over which you have no control. As the example below shows, *GearWrapper* allows your application to create a new instance of *Gear* using an options hash.

```ruby
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
```

#### Managing Dependency Direction ###

In the first example *Gear* depended on *Wheel* and now in the next example below *Wheel* depends on *Gear* or *ratio*. The next lesson examines how choosing dependency direction can mean the difference between a pleasing code-base and one that is harder and harder to change.

```ruby
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
```

##### How to choose Dependency Direction

Pretend for a moment that your classes are people. If you were to give them advice about how to behave you would tell them to *depend on things that change less often than you do.* This is based on the following simple truths about code:

>  - Some classes are more likely than others to have changes in requirements.
>  - Concrete classes are more likely to change than abstract classes.
>  - Changing a class that has many dependents will result in widespread consequences.

An example of an abstract class is when you inject *Wheel* into *Gear* such that *Gear* then depends on a *Duck* who responds to *diameter*, you are, however casually, defining an interface. This interface is an abstraction of the idea that a certain category of things will have a diameter. The abstraction was harvested from a concrete class; and depending on the abstraction is always safer than depending on a concretion because by its very nature, the abstraction is more stable.

#### Summary ###

Dependency management is core to creating future-proof applications. Injecting dependencies creates loosely coupled objects that can be reused in novel ways. Isolating dependencies allows objects to quickly adapt to unexpected changes. Depending on abstractions decreases the likelihood of facing these changes.

The key to managing dependencies is to control their direction.
