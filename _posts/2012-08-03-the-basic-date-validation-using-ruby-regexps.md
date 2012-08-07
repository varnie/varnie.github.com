---
layout: post
title: "The basic date validation using Ruby regexps"
description: ""
category: 
tags: [ruby]
---
{% include JB/setup %}

Hi there, Ruby lovers all over the world.

I've been playing with Ruby regexps for a while and worked out a usable regexp (well, to my opinion) which checks whether the date format is proper ("YYYY#MM#DD") where # stands for a separator: - or : or /.

It performs basic checks on a date: accepts only dates which have a valid year (which cannot start from zero), a valid month (from 01 to 12), and checks that a day is within the range \[1..31\].

The second separator must match the first.

One of the drawbacks of that regexp is that it is not smart enough to know whether a supplied day is valid for the supplied month, but it may be further improved by the use of Ruby's Date and DateTime modules.

Alrighty, let's look at the code!

{% gistnocache 1199866 dateCheck.rb %}

A curious reader might have already noticed I used lots of spaces in the regexp. Yes, it is possible in Ruby regexps if you specify 'x' option (as I did). It makes it much easier to understand. Effective comments also worth mentioning. Without them the regexp might not be so straightforward and clear.

Okay, let's get rid of the mentioned flaws. All we need is to look at Ruby's handy Date class which performs all these checks automatically during instantiation a new Date object.

Here is a final solution:

{% gistnocache 1200030 dateCheckUpdated.rb %}

It discards wrong entries such as "2011:11:31", "2011:02:29" etc.

Cheers!


