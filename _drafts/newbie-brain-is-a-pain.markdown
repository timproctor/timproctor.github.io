---
layout: post
title: "Newbie brain is a Pain"
date: 2014-10-26 13:51:46 -0600
comments: true
categories: problem-solving
---

## Learn how to problem solve

Jeff Casimir gave me good advice around the first week of July, 2014. He told me that I need
to sit down and figure out how to problem solve. At first, I thought it to be an absurd
idea. How does one sit down and learn how to problem solve?

## Symptoms that this applies to you

Here were some common newbie rabbit holes I was digging for myself:

- not reading error messages
- not looking for logs with error messages
- not solving the right problem
- looking too far ahead, out of scope given the current context
- thinking that I don't need help
- applying the wrong stackoverflow fix

## Not solving the right problem

This week I realize that I wasted so much time trying to solve a problem I didn't need to solve.
I was spending all this time trying to figure out how to run my assets from Amazon's
CloudFront CDN (content delivery network). I created key, path, and syntax problems
while trying to deploy my little simple application onto Heroku. Then I would fix my key,
path, and syntax problems and still I could not see the right results on Heroku.

## Another set of eyes

Finally, I sat down with a friend. I explained the steps I took, the error messages, and the
plan I had for fixing my problem. "Does your site upload images," my friend asked. "No,"
I replied. "Then why are you using a CDN in the first place?" my friend questioned further.

## Why?! indeed!

Rails 4 does a great job of precompiling your assets automatically to Heroku. The only reason
I would need a CDN would be to upload pictures or images. This was functionality that my
site does not even have yet. I fell prey to trying to solve a problem that I didn't need
to solve. Ten minutes later, I had my site up and running on Heroku.

## Lesson learned

I spent two days on a problem without really asking myself: "Why are you trying to solve
this problem?" The question I should have asked myself is: "Does having to go through a third
party CDN make sense every time you deploy to Heroku?" So, for me, sitting down and figuring
out how to be a better problem solver is not in the cards for me. Nor, do I really think anyone
can sit down and figure out how to problem solve. Jeff's point, I believe, was that I needed
to admit to myself that I sucked at solving programming problems. Just because I can solve a
myriad of life problems doesn't mean I just get it when it comes to programming.

## No Pain no gain

To really gain, I need to reflect on the lessons I learn. This blog is one way that I
accomplish this. Somehow, just writing about my experience of the last two days down,
aware no one will probably read it, is how I open up new pathways in my brain. The next
time something similar comes up, I will remember this pain and I will ask myself,
"Is this really a problem I need to solve?"
