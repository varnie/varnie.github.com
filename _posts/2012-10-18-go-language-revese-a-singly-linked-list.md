---
layout: post
title: "Go language: reverse a singly linked list"
description: "Go language: reverse a singly linked list"
keywords: golang
category: 
tags: [golang]
---
{% include JB/setup %}

Стало интересно, как развернуть односвязный список на языке <a href="http://http://golang.org" target="_blank">Go</a> без использования дополнительных списков, работы с памятью итд.

Решением с радостью делюсь со всеми кому интересно.

{% highlight go %}
package main

import "fmt"

type Item struct { 
    pNext *Item
    val rune
}

func createList() *Item {

    pHead := &Item{nil, 'a'}
    
    pCurr := pHead
    for i:= 'b'; i <= 'z'; i++ { 
        pItem := &Item{nil, i}       
        pCurr.pNext = pItem
        pCurr = pItem
    }
    
    return pHead
}

func printList(pList *Item) {

    pCurr := pList
    for {
        fmt.Printf("%c", pCurr.val)
        
        if pCurr.pNext != nil {
            pCurr = pCurr.pNext
        } else {
            break
        }
    }
    fmt.Println("")
}

func reverseList(pList *Item) *Item {
    
    pCurr := pList
    var pTop *Item = nil
    for {
        if pCurr == nil {
            break
        }
        pTemp := pCurr.pNext
        pCurr.pNext = pTop
        pTop = pCurr
        pCurr = pTemp        
    }
    
    return pTop
}

func main() {

    var pList = createList() 
    printList(pList)
    printList(reverseList(pList))
}
{% endhighlight %}
Вывод:
<pre class="terminal">
abcdefghijklmnopqrstuvwxyz
zyxwvutsrqponmlkjihgfedcba
</pre>
