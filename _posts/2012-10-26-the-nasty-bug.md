---
layout: post
title: "The nasty bug"
description: ""
keywords: "PHP"
category: 
tags: [php]
---
{% include JB/setup %}

Пятница, поздний вечер. Все идет хорошо, ничто не предвещает беду. Внезапно Вам сообщают, что галерея на Вашем сайте не работает. Хотя ведь работала, черть подери!!!
Судорожно смотришь в svn log, кто что делал за последние пол-дня. Хм, вроде бы чисто, никто не трогал галерею. Хотя...
В одном из базовых скриптов участвующих в формировании линков на AJAX-запросы для конкретной страницы видим:

{%highlight php%}
switch ($alias) {
    //много много кейсов
    //...
    case 'galleryData':
        $link = '/gallery.php?action=galleryData';
    
    case 'fooBarCheck':
        $link = '/main/foobarcheck';
        break;            
{%endhighlight%}

Кто-то похерил <b>break</b> у кейса '<b>galleryData</b>' и теперь даже если <b>$alias</b> равен <b>'galleryData'</b>, исполнение пойдет далее и <b>$link</b> переприсвоится и станет <b>'/main/foobarcheck'</b>.
Повезло, что проблему так быстро выявили и пофиксили.

Занятно, что в новомодных языках, таких как Go эту проблему решили на корню: проход сквозь несколько веток свитча запрещен на уровне языка. Если же требуется такое поведение (что случается крайне редко), то нужно использовать ключевое слово <b>fallthrough</b>:


<i>"In a switch statement, case labels do not fall through. You can make them fall through using the fallthrough keyword. This applies even to adjacent cases."</i>

В Perl же аналогичного поведения можно добиться используя явное указание ключевого слова <b>next</b> или же используя <b>use Switch 'fallthrough';</b>.
Линк на <a href="http://perldoc.perl.org/Switch.html#Allowing-fall-through" target="_blank" title="Perl docs">доку</a>.
