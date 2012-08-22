---
layout: post
title: "perl: parsing timestamps"
description: "Изучаю perl: регулярные выражения"
keywords: perl,timestamp,parse,parsing,regexp,regular expressions
category: 
tags: [perl]
---
{% include JB/setup %}

На форуме RSDN набрел на следующую задачу:

> Имеем лог с содержимым вида:
> *   Aug 20 2012 11:00:39 fhfh
> *   Aug 14 2012 12:39:27 bird
> *   Apr 16 2012 3:16:0 cat
>
> , где Aug 20 2012 11:00:39" - таймстамп, а "fhfh" - некие данные.
> Требуется написать perl программу, которая подсчитывает количество строк из лога, содержащих то или иное регулярное выражение за указанный промежуток времени в часах до текущего времени.
> Регулярное выражение и количество часов задается в аргументах программы.

{% highlight perl %}
#!/usr/bin/perl
use Modern::Perl;
use Time::Local qw(timelocal);
use Date::Parse qw(strptime);
use Scalar::Util::Numeric qw(isint);

#Please write a Perl script that will check a log file and count how many
#times a pattern appears within hh hours.
#For example...
#
#checklog.pl /home/saag/mylog.txt "created '\w[1-10]$" 48
#the file contains entries having the following format:
#Sep 11 2012 11:06:39 abc some text
if (@ARGV < 2) {
    die "Not enough arguments\n";
}

my ($regex_pattern, $hours_count) = splice(@ARGV, 0, 2);
my $regex = eval { qr/$regex_pattern/is };
if (!$regex) {
    die "malformed regex: $@";
}

if (!isint $hours_count) {
    die "malformed integer value for hours";
}

my $current_time = time();
my $from_time = $current_time - $hours_count * 60 * 60;

my $count_matched = 0;
while (<DATA>) {
    # take one input line at a time
    chomp;
    
    my $count = 0;
    1 while (/\s/gp && $count++ < 4);
    # modifier /p: Preserve the string matched such that ${^PREMATCH},
    # {$^MATCH}, and ${^POSTMATCH} are available for use after matching.

    if ($count == 4) {
        my ($date, $text) = (${^PREMATCH}, ${^POSTMATCH});
        
        my ($ss, $mm, $hh, $day, $month, $year, undef) = strptime($date);
        $year += 1900;
        my $time = timelocal($ss, $mm, $hh, $day, $month, $year);
        if ($time >= $from_time && $time < $current_time && $text =~ /$regex/) {
            say "found: `$_`";
            ++$count_matched;
        }
    }
}

say "total matches: $count_matched";
__DATA__
Aug 20 2012 11:00:39 fhfh
Aug 14 2012 12:39:27 bird
Apr 16 2012 3:16:0 cat
Nov 11 2012 18:25:56 dog
Jul 13 2012 12:9:46 fird
Jul 11 2012 23:43:24 bird
Feb 18 2012 1:48:34 fat
Nov 11 2012 10:22:50 fog
May 17 2012 10:42:17 fat
Aug 17 2012 10:13:18 fird
Jun 11 2012 20:15:50 fat
Oct 13 2012 16:37:23 fat
Jan 12 2012 23:35:19 cat
Nov 11 2012 21:39:44 dog
Sep 19 2012 22:16:50 dog
Aug 19 2012 11:00:39 fhfh
Jul 12 2012 14:31:35 cat
Apr 16 2012 14:41:39 dog
Oct 20 2012 3:40:12 bird
Jul 23 2012 3:25:35 bird
Oct 24 2012 16:18:5 cat
{% endhighlight %}

Вместо работы с логом из файла я переместил данные прямо в исходник, предварив их \__DATA\__, что очень удобно для демонстрации.
Подробнее об этой фиче можно почитать, обратившись к perldoc perldata.
