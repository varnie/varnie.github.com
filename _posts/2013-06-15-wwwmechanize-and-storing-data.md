---
layout: post
title: "WWW::Mechanize and keeping tracks of the performed requests"
description: "WWW::Mechanize и хранение данных предыдущих запросов"
keywords: "perl, mechanize, track, requests, store, disable"
category: 
tags: [perl]
---
{% include JB/setup %}

Столкнулся с проблемой при использовании WWW:Mechanize - после многократной отправки POST запросов с большими данными Perl падал с <pre class="terminal">Out of memory</pre>. 
Вот этот очень простой пример:

{% highlight perl %}
#!/usr/bin/perl

use strict;
use warnings;
use WWW::Mechanize;
use 5.010;

$| = 1;

my $mech = WWW::Mechanize->new(autocheck => 1);

foreach (1..1000) {
    $mech->post('http://SOME_URL_HERE', [some_action => 'SOME_ACTION_HERE',  some_data => join ('', map { int rand 2} (0..63)) x (16*1024*50)]);
    say $mech->response->decoded_content;
}
{% endhighlight %}

То бишь, 1000 раз шлем POST запрос со строчкой-мусором длиной в 50 мегабайт. Жирная строчка, да, но не в этом вся соль.
К примеру, на моей машине программка падала ближе к 50-ой итерации.

Нелогично и непонятно - по-хорошему память должна подчищаться после каждой итерации.

Первая мысль - на каждой итерации создавать новый инстанс WWW::Mechanize, но зачем городить огород? Давайте разберемся. На помощь придет хорошо всем знакомый Data::Dumper.
Итак, дебажный код:

{% highlight perl %}
#!/usr/bin/perl

use strict;
use warnings;
use WWW::Mechanize;
use 5.010;
use Data::Dumper;

$| = 1;

my $mech = WWW::Mechanize->new(autocheck => 1);

foreach (1..1000) {
    $mech->post('http://SOME_URL_HERE', [some_action => 'SOME_ACTION_HERE',  some_data => join ('', map { int rand 2} (0..63)) x (16*1024*50)]);
    say Dumper($mech);
    say $mech->response->decoded_content;
}
{% endhighlight %}

Запускаем в консоле и наблюдаем: чем дальше в лес - тем толще партизаны, т.е. чем больше прогнано итераций в цикле, тем длиннее вывод генерит дампер:).
Узкое место - поле "page_stack" (массив хранящий инфу о предыдущих прогнанных запросах)!!! Оно растёт с каждой новой итерацией.

Для нашего случая хранить эти данные абсолютно не нужно, поэтому идём на сайт <a href="http://search.cpan.org/~jesse/WWW-Mechanize-1.72/lib/WWW/Mechanize.pm#___top" title="WWW::Mechanize" target="_blank">WWW::Mechanize</a> и читаем:

<pre class="terminal">
stack_depth => $value

Sets the depth of the page stack that keeps track of all the downloaded pages. Default is effectively infinite stack size. If the stack is eating up your memory, then set this to a smaller number, say 5 or 10. Setting this to zero means Mech will keep no history.
</pre>

Итоговый, рабочий вариант, который не жрёт память аки конь:
{% highlight perl %}
#!/usr/bin/perl

use strict;
use warnings;
use WWW::Mechanize;
use 5.010;

$| = 1;

my $mech = WWW::Mechanize->new(autocheck => 1);
$mech->stack_depth(0); #do not keep track of the performed requests!

foreach (1..1000) {
    $mech->post('http://SOME_URL_HERE', [some_action => 'SOME_ACTION_HERE',  some_data => join ('', map { int rand 2} (0..63)) x (16*1024*50)]);
    say $mech->response->decoded_content;
}
{% endhighlight %}


Мораль - читать доки об используемых либах.
