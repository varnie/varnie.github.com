---
layout: post
title: "Learning perl: regular expressions."
description: "Изучаю перл: регулярные выражения"
category: 
tags: [perl]
---
{% include JB/setup %}

Неторопливо изучаю великую книгу **Learning Perl** авторов Randal L. Schwartz, Tom Phoenix, brian d foy и пробую на практике изученное.

В качестве упражнения для самоконтроля придумал следующую задачу:

"определить, содержит ли указанная строка элементы, разделенные одним и тем же разделителем и если да - вернуть разделитель. Два разделителя не могут идти одновременно."

Т.е к примеру для строки *q,w,e,r,t,y* результатом должна быть запятая.

Для строки *qwe!qer2452!wt* - восклицательный знак.

Для строки *a(b((* - ответ должен быть отрицательным, т.к. после второго разделителя, (, нет элемента и сразу идет третий разделитель. Можно было изменить условие задачи и считать такие строки подходящими, но я решил что такие ситуации не слишком полезны на практике.

И да, как я уже сказал, строка вида *234@3252@foobar!* не должна удовлетворять условиям задачи - т.к. здесь присутствуют 2 различных разделителя.

Итак, моё решение:

    #!/usr/bin/perl
    use Modern::Perl;

    print "please enter a test string, or q/Q for exit: ";
    chomp(my $input = <STDIN>);
     
    while (lc($input) ne "q") {    
        my $separator = find_separator($input);
        
        print "correct. separator is `$separator`\n" if $separator;
        print "please enter a test string, or q/Q for exit: ";
        chomp($input = <STDIN>);
    }

    sub find_separator {
        my $str = shift @_;
        
        my ($result, $separator);
        while ($str =~ s/^\w+(?<SEPARATOR>\W)//) {
            if ($separator && $separator ne $+{SEPARATOR}) {
                $result = 0;
                last;
            }
           
            ($separator, $result) = ($1, 1);
        }
       
        if (length $str > 0 && $str !~ /^\w+$/) {
            $result = 0;
        }
        
        $result ? $separator : undef;
    }

Если мы усложним немного условие задачи, запретив строки оканчивающиеся на разделитель (к примеру, *foo\**), то потребуется чуть-чуть изменить подпрограмму find_separator:

    sub find_separator {
        my $str = shift;
        
        my ($result, $separator);
        while ($str =~ s/^\w+(?<SEPARATOR>\W)//) {
            if ($separator && $separator ne $+{SEPARATOR} || length $str == 0) {    #added || length $str == 0
                $result = 0;
                last;
            }
           
            ($separator, $result) = ($1, 1);
        }
       
        if (length $str > 0 && $str !~ /^\w+$/) {
            $result = 0;
        }
        
        $result ? $separator : undef;
    }
    
Для вышеуказаной работы с подпрограммой *find_separator* этого достаточно, но что если мы попытаемся вызвать её без передачи строки? Мы получим warning:
> Use of uninitialized value $str in substitution (s///) at /home/varnie/foo/regexp.pl.

Для защиты от подобных неприятностей немного допилим подпрограмму *find_separator*:

    sub find_separator {
        return undef unless @_ > 0; #added
        
        my ($str, $result, $separator) = shift;
        while ($str =~ s/^\w+(?<SEPARATOR>\W)//) {
            if ($separator && $separator ne $+{SEPARATOR} || length $str == 0) {
                $result = 0;
                last;
            }
           
            ($separator, $result) = ($1, 1);
        }
       
        if (length $str > 0 && $str !~ /^\w+$/) {
            $result = 0;
        }
        
        $result ? $separator : undef;
    }
    
Теперь, даже если вызовем *find_separator()* без передачи ей строки, она отработает правильно.
