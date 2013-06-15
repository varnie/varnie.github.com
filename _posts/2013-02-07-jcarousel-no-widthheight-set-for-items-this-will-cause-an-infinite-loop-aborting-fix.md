---
layout: post
title: "jCarousel: No width/height set for items. This will cause an infinite loop. Aborting...: fix"
description: "jCarousel: No width/height set for items. This will cause an infinite loop. Aborting...: fix"
keywords: "jQuery, jCarousel, infinite loop, IE8 bug, fix"
category: 
tags: [js]
---
{% include JB/setup %} 

При использовании jQuery плагина jCarousel в IE8 (будь он проклят трижды) словил баг:

*"Сообщение: jCarousel: No width/height set for items. This will cause an infinite loop. Aborting...".*

Несмотря на то, что закастомайзенный класс ul/li карусельки имел явно прописанные CSS-аттрибуты width и height, IE8 упорно делал вид что не видит их, отсюда и баг.

Кто натолкнулся на этот баг, вот готовое решение: наряду со своими кастом-стилями прописать дефолтный ".jcarousel-list li { height: 39px; width: 44px; }", где значения, разумеется, должны быть вашими.

Надеюсь, кому-нибудь эта заметка пригодится.
