---
layout: post
title: "Befunge: first steps"
description: "Befunge, first steps, simple program"
keywords: ""
category: 
tags: []
---
{% include JB/setup %}

Буду краток. Поковырялся с ужасным эзотерическим языком <a href="http://en.wikipedia.org/wiki/Befunge" title="Befunge on Wiki" target="_blank">Befunge</a>, в итоге разродился простой программкой, выводящей числа от 1 до 10 (да-да, это не так просто сделать, как кажется!).
Фантастика, но <b>это</b> работает.

Программка:
{% highlight php %}
1: v     : <

   5
   2
   *
   `
@  _ :.:1+ ^
{% endhighlight %}

Вывод:
<pre class="terminal">
1 2 3 4 5 6 7 8 9 10
</pre>

<b>updated:</b>
Теперь перепишем то же самое, но немного усложним - величину, до которой выводить значения, будем запрашивать у юзера. Реализация:
{% highlight php %}
&11p  1: v   :   <
         1
         1
         g
         
         `       
      @  _ :.:1+ ^ 
{% endhighlight %}

Вывод:
<pre class="terminal">
Befunge-93 Interpreter/Debugger v2.23
20
1 2 3 4 5 6 7 8 9 10 11 12 13 14 15 16 17 18 19 20
</pre>

Т.е запросили выводить до 20; вывод корректный.

Начало положено, далее что-нибудь еще напишу в свободное время.
