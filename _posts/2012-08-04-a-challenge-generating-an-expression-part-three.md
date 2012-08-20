---
layout: post
title: "A challenge: generating an expression: part three"
description: "A challenge: generating an expression"
keywords: php,challenge,generating expression
category: 
tags: [php]
---
{% include JB/setup %}

Today I decided to refresh my knowledge of PHP (sick!). Here's a PHP verson of the problem that has been discussed few posts ago:
{% highlight php %}
<?php

$src = '{Пожалуйста|Просто} сделайте так, чтобы это {удивительное|крутое|простое} тестовое предложение '
    . '{изменялось {быстро|мгновенно} случайным образом|менялось каждый раз}';

while (preg_match("/\{([^\{\}]*)\}/", $src, $matches)){
$alt_items = explode("|", $matches[1]);
$item = $alt_items[array_rand($alt_items)];
$src = str_replace($matches[0], $item, $src);
}

print $src;
{% endhighlight %}
