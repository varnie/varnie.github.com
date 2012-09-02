---
layout: post
title: "perl: Goatse operator usage"
description: "example of the goatse operator use"
keywords: "perl, goatse, goatse operator"
category:
tags: [perl]
---
{% include JB/setup %}

Велик язык Perl, велик настолько, что многое можно переписать десятком других способов. Собственно, потому и девиз Perl - TMTOWTDI.
Например, ковыряя предыдущую программку на Перле, я обнаружил что одно действие можно переписать несколько иначе (надеюсь найти и др. способы со временем).
Итак, вот этот кусок:
{% highlight perl %}
1 while (/\s/gp && $count++ < 4);
{% endhighlight %}

Как можно переписать:
{% highlight perl %}
my $count =()= /\s/gp;
{% endhighlight %}

Да, так называемый "goatse operator". Не буду разглашать принцип его работы, т.к. мне самому доставило удовольствие разобраться с этим. Кому интересны подробности - можно почитать <a href="http://www.perlmonks.org/?node_id=527975" target="_blank" title="Goatse operator">дискуссию на perlmonks.org</a>
Далее, кому интересны выкладки по перфомансу: <a href="http://stackoverflow.com/questions/3991766/is-the-perl-goatse-secret-operator-efficient" target="_blank" title="Goatse operator">дискуссия на StackOverflow</a>
