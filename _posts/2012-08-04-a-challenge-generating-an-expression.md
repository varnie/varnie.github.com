---
layout: post
title: "A challenge: generating an expression"
description: ""
category: 
tags: [ruby]
---
{% include JB/setup %}

Okay, here it is: suppose you have a string containing some substrings separated by '|', and glued together into smaller chunks like {a|{b|c}}. Someone asks you to generate a complete sentence using random alternatives you have. A quick solution I've came up with is below:
{% highlight ruby %}
# coding: utf-8

src = '{Пожалуйста|Просто} сделайте так, чтобы это {удивительное|крутое|простое} тестовое предложение ' <<
    '{изменялось {быстро|мгновенно} случайным образом|менялось каждый раз}';
r = Regexp.new(/\{[^\{\}]*\}/)

while (match = r.match(src)) do
  match.length.times { |i|
    offsets = match.offset(i)
    chunk = src[offsets[0],offsets[1]-offsets[0]]
    bracelessChunk = chunk[1..-2] #remove leading '{' and trailing '}'
    alternatives = bracelessChunk.split("|")
    selectedAlternative = alternatives[rand(alternatives.size())]
    src[chunk]=selectedAlternative
  }
end

puts src
{% endhighlight %}
I have another solution (this time written in Java), which follows a different approach. I'll post it later.
