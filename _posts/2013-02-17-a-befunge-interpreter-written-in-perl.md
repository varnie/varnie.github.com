---
layout: post
title: "A Befunge interpreter written in Perl"
description: "A Befunge interpreter written in Perl"
keywords: "befunge, interpreter, esoteric, programming, language"
category: 
tags: []
---
{% include JB/setup %}

Написал простейший интерпретатор эзотерического языка программирования Befunge. Код доступен на github: <a href="https://github.com/varnie/befunge.pl" target="_blank" title="A Befunge interpreter">https://github.com/varnie/befunge.pl</a>, поддерживающий спецификацию "Befunge-93" (т.е без всяких расширений синтаксиса, обрабатываются первые 80x25 символов исходного кода программок, итд).

Запускается следующим образом:
<pre class="terminal">
chmod +x befunge.pl
befunge.pl ./my_befunge_program.bf
</pre>

Внимание, емкость стека никак не ограничена, а потому, упирается лишь в динамическую память, доступную для перла. Действует Перловый принцип - "нет никаких лимитов" (если перефразировать;)
