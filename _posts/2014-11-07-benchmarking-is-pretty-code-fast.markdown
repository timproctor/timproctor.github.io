---
layout: post
title: "Benchmarking: Is Pretty Code Fast?"
date: 2014-11-07 10:28:52 -0700
comments: true
categories: testing ruby advanced-beginnger refactoring
---

# From Exercism.io, A "FizzBuzz" Type Exercise

Below, is a bunch of my Raindrops code that I wanted to benchmark to see if the nice looking examples were also the faster examples.

### Testing Iteration 5:


```ruby
require 'benchmark'

class Raindrops
  def self.convert(drops)
    sound  = ''
    sound << 'Pling'    if drops % 3 == 0
    sound << 'Plang'    if drops % 5 == 0
    sound << 'Plong'    if drops % 7 == 0
    sound  = drops.to_s if sound.empty?
    sound
  end
end

Benchmark.bm do |x|
  x.report { 8000.times { Raindrops.convert (105)} }
  x.report { 8000.times { Raindrops.convert (105)} }
  x.report { 8000.times { Raindrops.convert (105)} }
  x.report { 8000.times { Raindrops.convert (105)} }
end
```
#### Results

![iteration_5](http://timproctor/github.io/images/iteration_5.png)

Average of the 4 real times (the last column of data) = 0.00805325 seconds

### Testing Iteration 4:

```ruby
require 'benchmark'

class Raindrops
  def self.convert(drops)
    sound  = ''
    sound += 'Pling'    if drops % 3 == 0
    sound += 'Plang'    if drops % 5 == 0
    sound += 'Plong'    if drops % 7 == 0
    sound  = drops.to_s if sound.empty?
    sound
  end
end

Benchmark.bm do |x|
  x.report { 8000.times { Raindrops.convert (105)} }
  x.report { 8000.times { Raindrops.convert (105)} }
  x.report { 8000.times { Raindrops.convert (105)} }
  x.report { 8000.times { Raindrops.convert (105)} }
end
```
#### Results

![iteration_4](./images/iteration_4.png)

Average of the 4 real times (the last column of data) = 0.007983 seconds

### Testing Iteration 1:

```ruby
require 'benchmark'

class Raindrops
  def self.convert(drops)
    if (drops % 3 == 0) && (drops % 7 == 0) && (drops % 5 == 0)
      "PlingPlangPlong"
    elsif (drops % 3 == 0) && (drops % 7 == 0)
      "PlingPlong"
    elsif (drops % 3 == 0) && (drops % 5 == 0)
      "PlingPlang"
    elsif (drops % 7 == 0) && (drops % 5 == 0)
      "PlangPlong"
    elsif drops % 7 == 0
      "Plong"
    elsif drops % 5 == 0
      "Plang"
    elsif drops % 3 == 0
      "Pling"
    else
      drops.to_s
    end
  end
end


Benchmark.bm do |x|
  x.report { 8000.times { Raindrops.convert (105)} }
  x.report { 8000.times { Raindrops.convert (105)} }
  x.report { 8000.times { Raindrops.convert (105)} }
  x.report { 8000.times { Raindrops.convert (105)} }
end
```
#### Results

![iteration_1](./images/iteration_1.png)

Average of the 4 real times (the last column of data) = 0.0032615 seconds

### Testing Iteration 2:

```ruby
require 'benchmark'

class Raindrops
  def self.convert(drops)
    case
    when divisible_by_3_7_5?(drops)
      "PlingPlangPlong"
    when divisible_by_3_7?(drops)
      "PlingPlong"
    when divisible_by_3_5?(drops)
      "PlingPlang"
    when divisible_by_7_5?(drops)
      "PlangPlong"
    when divisible_by_7?(drops)
      "Plong"
    when divisible_by_5?(drops)
      "Plang"
    when divisible_by_3?(drops)
      "Pling"
    else
      drops.to_s
    end
  end

  private

  def self.divisible_by_3_7_5?(drops)
    (drops % 3 == 0) && (drops % 7 == 0) && (drops % 5 == 0)
  end

  def self.divisible_by_3_7?(drops)
    (drops % 3 == 0) && (drops % 7 == 0)
  end

  def self.divisible_by_3_5?(drops)
    (drops % 3 == 0) && (drops % 5 == 0)
  end

  def self.divisible_by_7_5?(drops)
    (drops % 7 == 0) && (drops % 5 == 0)
  end

  def self.divisible_by_7?(drops)
    (drops % 7 == 0)
  end

  def self.divisible_by_5?(drops)
    (drops % 5 == 0)
  end

  def self.divisible_by_3?(drops)
    (drops % 3 == 0)
  end

end


Benchmark.bm do |x|
  x.report { 8000.times { Raindrops.convert (105)} }
  x.report { 8000.times { Raindrops.convert (105)} }
  x.report { 8000.times { Raindrops.convert (105)} }
  x.report { 8000.times { Raindrops.convert (105)} }
end

```
#### Results

![iteration_2](./images/iteration_2.png)

Average of the 4 real times (the last column of data) = 0.015414 seconds

### Testing Iteration 6:
```ruby
require 'benchmark'


class Raindrops
  def self.convert(drops)
    sound = "#{pling(drops)}#{plang(drops)}#{plong(drops)}"
    sound.empty? ? drops.to_s : sound
  end

  def self.pling(drops)
    sound_made('Pling', drops, 3)
  end

  def self.plang(drops)
    sound_made('Plang', drops, 5)
  end

  def self.plong(drops)
    sound_made('Plong', drops, 7)
  end

  def self.sound_made(sound, drops, number)
    sound if (drops % number).zero?
  end
end



Benchmark.bm do |x|
  x.report { 8000.times { Raindrops.convert (105)} }
  x.report { 8000.times { Raindrops.convert (105)} }
  x.report { 8000.times { Raindrops.convert (105)} }
  x.report { 8000.times { Raindrops.convert (105)} }
end

```
#### Results

![iteration_6](./images/iteration_6.png)

Average of the 4 real times (the last column of data) = 0.01015875 seconds

### Testing Iteration 7:
```ruby
require 'benchmark'

class Raindrops
  def self.convert(number)
    (sound = divisible?("", number)) == "" ? number.to_s : sound
  end

  def self.divisible?(sound, number)
    {3 => 'Pling', 5 => 'Plang', 7 => 'Plong'}.each do |key, value|
      sound << value if number % key == 0
    end
    sound
  end

end

Benchmark.bm do |x|
  x.report { 8000.times { Raindrops.convert (105)} }
  x.report { 8000.times { Raindrops.convert (105)} }
  x.report { 8000.times { Raindrops.convert (105)} }
  x.report { 8000.times { Raindrops.convert (105)} }
end

```
#### Results

![iteration_7](./images/iteration_7.png)

Average of the 4 real times (the last column of data) = 0.0182455 seconds

### Conclusions

I feel like I should have done the experiments based on a much larger number of repeats, like 80,000
versus 8,000 times. If I make conclusions on these results, it looks like pretty code doesn't
necessarily mean faster code.

I also have a hunch that the Raindrops class is just not complex enough to be a valid measure
of anything.

In conclusion, I am not able to answer yet whether "elegant" Ruby is fast Ruby. Perhaps code readability is
a metric that trumps performance.

Somehow, I feel like I have more questions than when I started.
