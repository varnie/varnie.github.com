---
layout: post
title: "PHP: странности"
description: "PHP, Rust, Perl"
keywords: "PHP, Rust, perl"
category: 
tags: [php, Rust, perl]
---
{% include JB/setup %}

Нашел интересный <a href="http://stackoverflow.com/questions/11227809/why-is-processing-a-sorted-array-faster-than-an-unsorted-array" target="_blank">пост на stackoverflow</a> касаемо т.н. "Branch prediction". Примеры на С++, Яве, С#, Go есть, но захотелось потестить локально Perl, PHP и новый язык от компании Mozilla - <a href="http://www.rust-lang.org" target="_blank">Rust</a>. 

О них и поговорим ниже.

Первым идет переписка программы на Perl:
{% highlight perl %}
#!/usr/bin/perl
use strict;
use Modern::Perl;

my $array_size = 32768;
my @data;

#prepare data
for (my $c = 0; $c < $array_size; ++$c) {
    $data[$c] = int(rand(256)) + 1;
}
 
sub calc {
    my ($data_ref, $times) = (shift, shift);
    
    my $sum = 0;
    for (my $i = 0; $i < $times; ++$i) {
        for my $v (@$data_ref) {
            if ($v >= 128) {
                $sum += $v;
            }
        }
    }
    
    return $sum;
} 

say "estimate with not sorted array";
my $start = time();
my $result = calc(\@data, 100000);
my $elapsed = time() - $start;
say "sum: $result";
say "elapsed time: $elapsed seconds";


say "estimate with sorted array";
my @sorted_data = sort { $a <=> $b } @data;

$start = time();
$result = calc(\@sorted_data, 100000);
$elapsed = time() - $start;
say "sum: $result";
say "elapsed time: $elapsed seconds";
{% endhighlight %}

Ниже аналогично на PHP:
{% highlight php %}
<?php

function calc(&$arr, $times) {

    $sum = 0;
    
    for ($i = 0; $i < $times; ++$i) {
        foreach ($arr as $v) {
            if ($v >= 128) {
                $sum += $v; 
            }
        } 
    }
    
    return $sum;
}

//prepare array
$array_size = 32768;
$data = array();

for ($c = 0; $c < $array_size; ++$c) {
    $data[] = rand(1, 256);
}

print "estimate with not sorted array\n";
$start = time();
$sum = calc($data, 100000);
$elapsed = time() - $start;
echo "elapsed time: " . $elapsed . " seconds\n";
echo "sum: " . $sum . "\n";

print "estimate with sorted array\n";
sort($data);
$start = time();
$sum = calc($data, 100000);
$elapsed = time() - $start;
echo "elapsed time: " . $elapsed . " seconds\n";
echo "sum: " . $sum . "\n";
{% endhighlight %}

и на закуску экзотика в лице языка Rust:
{% highlight php %}
extern mod std;
use rand = core::rand;

fn calculate(d: &[int], iterations_cnt: uint) -> u64 {

    let mut sum: u64 = 0 as u64;
    
    for iterations_cnt.times  {
        for d.each |val| {                 
            if *val >= 128 {
                sum += *val as u64;
            } 
        }
    }
    
    return sum;
}

fn main() {

    //prepare data
    let array_size = 32768;
    let mut data: ~[int] = ~[];
    vec::reserve(&mut data, array_size);
    
    let r = rand::Rng();
    for array_size.times {
        data.push(r.gen_int_range(1, 256) + 1);
    }

    let iterations_count = 100000;

    io::println("estimate with not sorted array");    
    let mut start = std::time::precise_time_s();
    let mut result = calculate(data, iterations_count);
    let mut stop = std::time::precise_time_s();
    io::println(u64::str(result));
    io::println(#fmt("elapsed time: %f ms", (stop - start) * 1000f));
    
    pure fn leual(a: &int, b: &int) -> bool { *a <= *b }
    let sorted_data = std::sort::merge_sort(leual, data);
    io::println("estimate with sorted array");
    start = std::time::precise_time_s();
    result = calculate(sorted_data, iterations_count);
    stop = std::time::precise_time_s();
    io::println(u64::str(result));
    io::println(#fmt("elapsed time: %f ms", (stop - start) * 1000f));
}
{% endhighlight %}

Также для полноты картины и для того, чтобы было с чем сравнивать, прогнал исходную программу на С++ у себя на компе.
На моем Core2Duo E6600 2.4 GHZ и с 4 гигами оперативки имеем сл. результаты:

<b>C++ (gcc version 4.4.5)</b>

estimate with not sorted array

314931600000

elapsed time: 27.63 sec

estimate with sorted array

314931600000

elapsed time: 11.11 sec

<b>Go (version go1)</b>

estimate with not sorted array

317447800000

elapsed time: 24.855818 sec 

estimate with sorted array

317447800000

elapsed time: 13.112228s

<b>Rust (rustc 0.4 (39c0d35 2012-10-11 21:01:16 -0700))</b>:

estimate with not sorted array

321315600000

elapsed time: 360.734 ms

estimate with sorted array

321315600000

elapsed time: 356.501 ms

<b>PHP (PHP 5.3.3-1ubuntu9.10 with Suhosin-Patch)</b>:

estimate with not sorted array

elapsed time: 1210 seconds

sum: 316930100000

estimate with sorted array

elapsed time: 1135 seconds

sum: 316930100000

<b>Perl (v5.10.1)</b>:

estimate with not sorted array

sum: 317376400000

elapsed time: 553 seconds

estimate with sorted array

sum: 317376400000

elapsed time: 500 seconds

</br>

Поигрался с оптимизациями, в итоге результаты следующие:

<b>C++ (gcc version 4.4.5, пример билдил с флагом -O3)</b>

estimate with not sorted array

314931600000

elapsed time: 18.86 sec

estimate with sorted array

314931600000

elapsed time: 7.74 sec

<b>Go (version go1, пример билдил с -gccgoflags '-O3')</b>

estimate with not sorted array

317447800000

elapsed time: 24.956293s

estimate with sorted array

317447800000

elapsed time: 13.127326s

<b>Rust (rustc 0.4, пример билдил с флагом --opt-level=3 (39c0d35 2012-10-11 21:01:16 -0700))</b>:

estimate with not sorted array

320484300000

elapsed time: 20333.950295 ms

estimate with sorted array

320484300000

elapsed time: 7764.199734 ms<br/>
Результаты PHP выглядят просто ужасно, даже Perl его обгоняет.
