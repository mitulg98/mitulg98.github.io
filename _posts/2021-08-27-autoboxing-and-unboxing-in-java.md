---
layout: post
title:  "Boxing and unboxing in Java"
---

The coexistence of primitive types and reference types in Java is something to ponder about. For certain situations like collections Java doesn't allow the usage of primitive types. Also we cannot perform arithmetics over references types. This gives rise to the need of boxing and unboxing. Now we very well know that boxing means conversion of primitive type to reference type (i.e. from `int` to `Integer`) and unboxing is vice-versa (`Integer` to `int`). Now taking care of boxing and unboxing on your own is quite painful. Hence Java provides us with autoboxing and unboxing. Let's consider a simple example to see how it makes our lives easier :

{% highlight java %}
public class MyClass {
    Map<Character, Integer> getFrequencyOfEachCharacter(String word) {
        Map<Character, Integer> frequency = new Map<>();
        for(int i = 0; i < word.length(); i++) {
            int newFrequency = frequency.get(word.charAt(i)) + 1;
            frequency.put(word.charAt(i), newFrequency);
        }
        return frequency;
    }
}
{% endhighlight %}

Let's analyze above code for the events of autoboxing and unboxing :

1. `word.charAt(i)` gives a `char` type which is autoboxed to `Character` type when `frequency.get()` is called.

2. `frequency.get()` returns an `Integer` object which is autounboxed to `int`.

3. When `frequency.put()` is called then both the primitive values `word.charAt(i)` and `newFrequency` are autoboxed to their reference types i.e. `Character` and `Integer`.

From what I got to know through my research internally autoboxing and unboxing are implemented in the following manner :

* _Autoboxing_ : `Integer.valueOf()` is called for this. Creating a new `Integer` object could be an option but it has its own overhead and also unlike the former it doesn't allow caching in JVM.

* _Autounboxing_ : `Integer.intValue()` is used for this.

Regarding the caching with `Integer.valueOf()` let's consider the below code block :

{% highlight java %}
Integer num1 = new Integer(1);
Integer num2 = new Integer(1);

System.out.println(num1 == num2); // Returns false

Integer num3 = Integer.valueOf(1);
Integer.num4 = Integer.valueOf(1);

System.out.println(num3 == num4); // Returns true
{% endhighlight %}

As shown above `Integer.valueOf()` can use objects cached inside the JVM and hence performance improvement. However this has a bad side too. Only first few values closest to 0 are cached (on my system from -128 to 127) and rest are not. So don't be surprised if you get something like :

{% highlight java %}
Integer num3 = Integer.valueOf(1);
Integer num4 = Integer.valueOf(1);

System.out.println(num1 == num2); // Returns true since was cached

Integer num3 = Integer.valueOf(500);
Integer num4 = Integer.valueOf(500);

System.out.println(num3 == num4); // Returns false since not cached
{% endhighlight %}

Coming back to autoboxing and unboxing it's clear that these features have made the difference between primitve and reference types almost non-feeble. But there is always a performance cost of boxing and unboxing. Also using reference types has an additional overhead of creating new objects. Therefore use reference types only when it's required and not as a default choice.

Happy Coding !