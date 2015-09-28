---
layout: post
title: "POODR by Sandi Metz Chapter Three"
date: 2015-03-27 13:39:12 -0600
comments: true
categories:
---
## Book-Review: Chapter Three (Managing Dependencies)

###### DISCLAIMER/Acknowledement:
If there are any sentences or phrases that make a lot of sense to you then it was
most likely copied word for word from Sandi's book. She continues to inspire.

#### Purpose:
To have the highest success at learning anything, the experts agree that one should
teach the intended content as opposed to applying a study technique.

For example, the Pythagorean Theorem is better retained by teaching it to a co-worker versus
making a `(a * a) + (b * b) == c * c` flash-card.

The purpose of this blog is to learn as much as possible from Sandi Metz's
amazing book, *Practical Object-Oriented Design in Ruby* by teaching it to you, the
reader.

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

```
