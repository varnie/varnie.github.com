---
layout: post
title: "A challenge: generating an expression: part two"
description: ""
category: 
tags: [java]
---
{% include JB/setup %}

Hi all.

As I promised few days ago, I'm showing one more solution to that problem.

Here's the code:
{% highlight java %}
import java.text.CharacterIterator;
import java.text.StringCharacterIterator;
import java.util.ArrayList;
import java.util.List;
import java.util.Random;
import java.util.Stack;

public class SentenceMixer {

    private static class Item {

        private List<Object> objects = new ArrayList<Object>();

        public Item(String value) {
            add(value);
        }

        public Item() {
            super();
        }

        public void add(Object value) {
            objects.add(value);
        }

        public void shrink() {
            Random randomGenerator = new Random();

            int preserveIndex = randomGenerator.nextInt(objects.size());
            for (int i = objects.size() - 1; i >= 0; --i) {
                if (i != preserveIndex) {
                    objects.remove(i);
                }
            }
        }

        public void archive() {

            int count = objects.size();
            if (count == 2) {
                shrink();
            } else if (count > 2) {
                Item item = new Item();
                for (Object obj : objects) {
                    item.add(obj);
                }
                objects.clear();
                objects.add(item);
            }
        }
    }

    private final String sentence;

    public SentenceMixer(String sentence) {
        this.sentence = sentence;
    }

    public String generate() {
        Stack<Item> stack = new Stack<Item>();

        stack.push(new Item());
        Item top = stack.peek();
        StringBuilder stringBuilder = new StringBuilder();

        CharacterIterator iter = new StringCharacterIterator(sentence);
        for (char ch = iter.first(); ch != CharacterIterator.DONE; ch = iter.next()) {
            if (ch == '|') {
                if (stringBuilder.length() > 0){
                    top.add(new Item(stringBuilder.toString()));
                    stringBuilder.setLength(0);
                    top.archive();
                }
            } else if (ch == '{') {
                if (stringBuilder.length() > 0) {
                    top.add(new Item(stringBuilder.toString()));
                    stringBuilder.setLength(0);
                }
                Item item = new Item();
                stack.add(item);
                top.add(item);
                top = item;
            } else if (ch == '}') {
                if (stringBuilder.length() > 0) {
                    top.add(new Item(stringBuilder.toString()));
                    stringBuilder.setLength(0);
                }
                top.shrink();
                stack.pop();
                top = stack.peek();
            } else {
                stringBuilder.append(ch);
            }
        }

        return traverseItem(top);
    }

    private static String traverseItem(Item item) {

        StringBuilder stringBuilder = new StringBuilder();

        for (Object obj : item.objects) {
            if (obj instanceof String) {
                stringBuilder.append(obj);
            } else {
                stringBuilder.append(traverseItem((Item) obj));
            }
        }

        return stringBuilder.toString();
    }

    public static void main(String[] args) {
        System.out.println(new SentenceMixer("{Пожалуйста|Просто} сделайте так, чтобы это {удивительное|крутое|простое} " + 
            + "тестовое предложение {изменялось {быстро|мгновенно} случайным образом|менялось каждый раз}.").generate());
    }
}
{% endhighlight %}
