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

{%highlight php startinline%}
switch ($alias) {
    //много много кейсов
    //...
    case 'galleryData':
        $link = '/gallery.php?action=galleryData';
    
    case 'fooBarCheck':
        $link = '/main/foobarcheck';
        break;            
{%endhighlight%}

Кто-то похерил __break__ у кейса __'galleryData'__ и теперь даже если __$alias__ равен __'galleryData'__, исполнение пойдет далее и __$link__ переприсвоится и станет __'/main/foobarcheck'__.

Повезло, что проблему так быстро выявили и пофиксили.

Занятно, что в новомодных языках, таких как Go эту проблему решили на корню: проход сквозь несколько веток свитча запрещен на уровне языка. Если же требуется такое поведение (что случается крайне редко), то нужно использовать ключевое слово __fallthrough__:


_"In a switch statement, case labels do not fall through. You can make them fall through using the fallthrough keyword. This applies even to adjacent cases."_

В Perl же аналогичного поведения можно добиться используя явное указание ключевого слова __next__ или же используя __use Switch 'fallthrough';__.
Линк на <a href="http://perldoc.perl.org/Switch.html#Allowing-fall-through" target="_blank" title="Perl docs">доку</a>.
