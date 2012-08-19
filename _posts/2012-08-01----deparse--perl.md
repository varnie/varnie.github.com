---
layout: post
title: "Пример работы модуля Deparse для perl"
description: "небольшой очерк на тему использования Deparse"
category: 
tags: [perl]
---
{% include JB/setup %}
На простом примере разобрался как работать с <a href="http://perldoc.perl.org/B/Deparse.html" target="_blank" title="Deparse">модулем Deparse</a> для изучения более детально синтаксического вида разобранной программы, как видит её perl.

Итак, имеем кривую программку:
{% highlight perl %}
#!/usr/bin/perl    
use Modern::Perl;

my $i = 1;
while ($i & 8 == 0 || $i & 256 == 0){
    ++$i;
}
print $i, "\n";
{% endhighlight %}
, которая на выводе даёт единицу. Если проблема слёту не видна, обратимся к возможностям <a href="http://perldoc.perl.org/B/Deparse.html" target="_blank" title="Deparse">модуля Deparse</a>:

varnie@foo:~/bar$ perl -MO=Deparse  /home/varnie/buggy.pl
{% highlight perl %}
use Modern::Perl;
use warnings;
use strict 'refs';
BEGIN {
    $^H{'feature_say'} = q(1);
    $^H{'feature_state'} = q(1);
    $^H{'feature_switch'} = q(1);
}
my $i = 1;
while ($i & !1 or $i & 0) {
    ++$i;
}
print $i, "\n";
{% endhighlight %}
/home/varnie/buggy.pl syntax OK

Здесь видно каким образом перловский парсер её видит; налицо ошибка.
Правим:
{% highlight perl %}
#!/usr/bin/perl
use Modern::Perl;

my $i = 1;
while (!($i & 8) || !($i & 256)){
    ++$i;
}

print $i, "\n"
{% endhighlight %}
Запускаем - 264. Все ОК. Для интереса запускаем с модулем Deparse:
varnie@foo:~/bar$ perl -MO=Deparse  /home/varnie/fixed.pl
{% highlight perl %}
use Modern::Perl;
use warnings;
use strict 'refs';
BEGIN {
    $^H{'feature_say'} = q(1);
    $^H{'feature_state'} = q(1);
    $^H{'feature_switch'} = q(1);
}
my $i = 1;
while (not $i & 8 or not $i & 256) {
    ++$i;
}
print $i, "\n";
{% endhighlight %}
/home/varnie/fixed.pl syntax OK

Видно что условие в цикле соответствует тому, что мы и пытались выполнить в перле.   
