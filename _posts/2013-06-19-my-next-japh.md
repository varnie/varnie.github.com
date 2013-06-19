---
layout: post
title: "My next JAPH"
description: "Ещё один JAPH"
keywords: "perl,JAPH"
category: 
tags: [perl]
---
{% include JB/setup %}

Написал еще один незамысловатый JAPH. Сначала покажу итоговую версию:

{% highlight perl %}
use strict;
 use warnings;
  my$k='my@a=((-4
   ,26,10,-4,-104,-
    56,-61,-79,-94,-
     127,-152,-162,-
      268,-245,-250,-
       264,-298,-403,-
        361,-399,-429,-
         454,-494,-516),
          @_);join"",map{
           chr$a[$_]+$a[$_+
            $#a/2+1]}0..$#a/2
             ';foreach(reverse(
               12..35)){$k="sub
                {$k}->(\@_,int 
                 map{(int)x\$_}(
                  1..$_))"}print
                   eval $k;
{% endhighlight %}

Можно было бы даже сократить немного код, убрав прагмы strict и warnings, но я сторонник чистоты кода и отсутствия ошибок.
Теперь то же самое, только в ужатом виде:

{% highlight perl %}
#!/usr/bin/perl

use strict;
use warnings;

my$k='my @a = ((-4, 26, 10, -4, -104, -56, -61, -79, -94, -127, -152, -162, -268, -245, -250, -264, -298, -403, -361, -399, -429, -454, -494, -516), @_);
join "", map{ chr $a[$_] + $a[$_+$#a/2+1] } 0..$#a/2';

foreach (reverse (12..35)) {
    $k = "sub{$k}->(\@_,int map{(int)x\$_}(1..$_))"
}

print eval $k;
{% endhighlight %}

Как работает код? Код генерит длиннющую строку с кодом, которой потом делается eval и print собственно результата. Весь финт в генерации этой строки-кода. О ней и поговорим ниже.
Т.е. по сути в результате у нас будет такая вот красота:

{% highlight perl %}

use strict;
use warnings;

print sub{
    sub{
        sub{
            sub{
                sub{               
                    sub{                       
                        sub{                          
                            sub{
                                sub{
                                    sub{
                                        sub{
                                            sub{
                                                sub{
                                                    sub{
                                                        sub{
                                                            sub{
                                                                sub{
                                                                    sub{
                                                                        sub{    
                                                                            sub{ 
                                                                                 sub{ 
                                                                                    sub{ 
                                                                                        sub{ 
                                                                                           sub{ 

    my (@a) = ((-4, 26, 10, -4, -104, -56, -61, -79, -94, -127, -152, -162, -268, -245, -250, -264, -298, -403, -361, -399, -429, -454, -494, -516), @_);
    print join "", map{ chr $a[$_] + $a[$_+$#a/2+1] } 0..$#a/2
                                                                                            }->(@_, int map{(int) x$_} (1..35)) 
                                                                                        }->(@_, int map{(int) x$_} (1..34)) 
                                                                                    }->(@_, int map{(int) x$_} (1..33)) 
                                                                                }->(@_, int map{(int) x$_} (1..32)) 
                                                                            }->(@_, int map{(int) x$_} (1..31)) 
                                                                        }->(@_, int map{(int) x$_} (1..30)) 
                                                                    }->(@_, int map{(int) x$_} (1..29)) 
                                                                }->(@_, int map{(int) x$_} (1..28)) 
                                                            }->(@_, int map{(int) x$_} (1..27)) 
                                                        }->(@_, int map{(int) x$_} (1..26)) 
                                                    }->(@_, int map{(int) x$_} (1..25)) 
                                                }->(@_, int map{(int) x$_} (1..24)) 
                                            }->(@_, int map{(int) x$_} (1..23)) 
                                        }->(@_, int map{(int) x$_} (1..22)) 
                                    }->(@_, int map{(int) x$_} (1..21)) 
                                }->(@_, int map{(int) x$_} (1..20)) 
                            }->(@_, int map{(int) x$_} (1..19)) 
                        }->(@_, int map{(int) x$_} (1..18)) 
                    }->(@_, int map{(int) x$_} (1..17)) 
                }->(@_, int map{(int) x$_} (1..16)) 
            }->(@_, int map{(int) x$_} (1..15))
        }->(@_, int map{(int) x$_} (1..14))
    }->(@_, int map{(int) x$_} (1..13))
}->(int map{(int) x $_} (1..12))
{% endhighlight %}

Дальше уже проще, т.ч разбор полётов оставлю читателю.
