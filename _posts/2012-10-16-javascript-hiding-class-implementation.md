---
layout: post
title: "JavaScript: hiding class implementation"
description: "JavaScript: сокрытие декларации классов"
keywords: "js"
category: 
tags: [js]
---
{% include JB/setup %}

Открыл для себя простой удобный способ спрятать реализацию класса в JavaScript. Сначала покажу код:

{% highlight javascript %}
var myFooObj = (function(){

    var FooClass = function() {};
    FooClass.prototype = { 
        //some data
    };

    return new FooClass(); 
})();
{% endhighlight %}

Как видно, вся хитрость в самовызываемой анонимной функции. Никто не запретит передать в класс какие-либо данные для инициализации, например:
{% highlight javascript %}
var myFooObj = (function(val){

    var FooClass = function() {}; 
    FooClass.prototype = { 
        value: val
    };

    return new FooClass(); 
})(43);

console.log(myFooObj.value); //43
{% endhighlight %}

Здесь уже сработает механизм замыканий.

Мы можем пойти дальше и реализовать наследование:
{% highlight javascript %}
var myChildObj = (function(){

    var ParentClass = function() {}; 
    ParentClass.prototype = { 
        //some data
    };

    function extendsClass(childClass, parentClass, isRewrite) {
        for(var property in parentClass.prototype) {
            if (isRewrite || 
               (!isRewrite && 
                !isDefined(childClass.prototype[property]))) {                         
                childClass.prototype[property] = parentClass.prototype[property];
            }
        }

        childClass.prototype.parent =  parentClass.prototype;
    }

    var ChildClass = function() {}; 
    ChildClass.prototype = {
        //some data
    }

    extendsClass(ChildClass, ParentClass, false);
    return new PhotoGallerySingle(); 
})();
{% endhighlight %}

Проблема всплывает в случае если имплементации классов слишком большие. В этом случае по-просту будет неэстетично постить "жирные" имплементации в одну функцию, но все же, вариант рабочий.
