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

#### Remove Argument-Order Dependencies

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
