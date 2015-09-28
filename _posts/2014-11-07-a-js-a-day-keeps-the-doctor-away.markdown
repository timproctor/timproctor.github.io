---
layout: post
title: "JS a day keeps the doctor away"
date: 2014-11-07 09:08:09 -0700
categories: javascript language
---
# Baby Steps To JavaScript

Ruby is my first programming language, my first love as well. We have been together for over a year
now. I remember how we first met on Codecademy, it has been love every since. There have been tears
along the way, arguments, disappointments, but I have remained faithful. I gave a lightning talk on
Elixir but Ruby understood. She would agree that José Valim has her best interests, she's not
threatened by Elixir. Besides, it looks a lot like her, it's just functional.

## JavaScript the alpha male

Recently, I've been hanging around JavaScript. I'm reminded of living with my older cousin, who
would berate me on how I loaded the top rack of the dishwasher incorrectly, while he was perfectly
content to sit on the couch surrounded by empty beer bottles. But my cousin and I would have a blast
on Friday night, going to Lady Arm Wrestling Competition Night at the local pub near the Space Needle.
JavaScript can be a lot of fun too when you really want amazing front-end things to happen with your
application.

## Necessary to learn

So, don't tell Ruby that I've been giving myself headaches while trying to learn JavaScript. Here is an
example that I copied. Yes, copied. I'm not even to the point where I can write JS on my own
without help. I'm just reading and copying it for now.

```javascript
var firstList  = ["a", "b", "c", "d", "e", "f"];
var secondList = [ "c", "e", "f"];

function remove_elements(arg1, arg2) {
  for(i = 0; i < arg2.length; i++) {
    arg1.splice(arg1.indexOf(arg2[i]), 1)
  }
  consolelog(arg1);
}
remove_elements(firstList, secondList);
```
