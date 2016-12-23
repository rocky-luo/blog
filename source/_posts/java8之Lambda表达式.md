---
title: java8之Lambda表达式
date: 2016-12-16 14:41:43
tags: [java8, lambda]
categories: java8
comments: true
---

java8里面引入了Lambda表达式，最近看了双十一买的《java8实战》里面有关Lambda的内容，列一下有关Lambda的几个要点，有助于其他学习者能快速地入门。

首先写一个Lambda的例子：

```java
public class Apple {
    private int weight;
    private String color;

    public Apple(int weight, String color) {
        this.weight = weight;
        this.color = color;
    }

    public static void filter(Apple apple,Predicate<Apple> predicate) {
        if (predicate.test(apple)){
            System.out.println("this apple is a good one");
        }
    }
    public static void main(String[] args) {
        BiFunction<Integer, String, Apple> supplier = (w, c) -> new Apple(w, c);
        Apple.filter(supplier.apply(50, "red"), (a) -> a.weight > 30);
    }
}
```

上面的Apple对象里面有一个filter方法，该方法用于判断某个苹果是否是好苹果

基本上有经验的人，大概看了上面这段代码，能够猜出来，苹果的重量 > 30 的苹果是好苹果，下面就根据这段代码来具体看一看Lambda表达式

### 函数描述符

函数描述符基本上就是函数的签名，但是在java里面函数签名应该是不包含函数的返回值的，也就是说两个名字相同，参数也相同，但是返回值不同的方法实际上是不能同时存在的，因为他们函数签名一样，但是在Lambda表达式里面，函数描述符包括函数的参数和返回值，举个例子,上面的

```
(w, c) -> new Apple(w, c);
```

实际上省略和函数参数类型，完整的写法应该是

```
(int w, String c) -> new Apple(w, c);
```

函数签名其实就是(int, String) -> Apple,意思是函数参数是int和String，返回值是Apple，之所以能省略，是应为通过BiFunction里面的方法能够推断出这些参数是什么类型，同理， 

```
(a) -> a.weight > 30
```

的函数签名是(Apple)->boolean

### 函数式接口

看了上面的代码，当时我就有个疑问是一个方法是怎么能够转换成一个类的。实际上在java8里面新增了一个函数式接口的概念，函数式接口的意思就是只有一个抽象方法的接口，这样的话一个方法如果和该函数式接口的抽象方法有一样的函数描述符的话，方法就可以转化成一个类。

java8里面接口里面的方法有了较大的变化，接口里的方法可以有默认的实现，用**default**关键字修饰，还可以有静态方法。。。

比如我们可以写一个这样的接口

```java
@FunctionalInterface
public interface Facade {
    void interface1();
    static void interface2() {
        System.out.println("this is static fun");
    }
    default void interface3() {
        System.out.println("this is default interface");
    }
}
```

**@FunctionalInterface**注解的意思是表明这是一个函数式接口, **@Override**类似

### 方法引用

如果我已有一个方法，而我又想在某个地方传递这个方法，就需要用到方法引用，比如说最开始的代码，我们可以通过方法引用改写为：

```java
public class Apple {
    private int weight;
    private String color;

    public Apple(int weight, String color) {
        this.weight = weight;
        this.color = color;
    }

    public static void filter(Apple apple,Predicate<Apple> predicate) {
        if (predicate.test(apple)){
            System.out.println("this apple is a good one");
        }
    }
    public static void main(String[] args) {
        BiFunction<Integer, String, Apple> supplier = Apple::new;
        Apple.filter(supplier.apply(50, "red"), Apple::GoodWeight);
    }

    private static boolean GoodWeight(Apple a) {
        return a.weight > 30;
    }
}
```

**Apple::new**是构造函数的引用，**Apple::GoodWeight**是方法**boolean GoodWeight(Apple a)**的引用

关于方法引用这块，还有其他几种情况，我自己的感觉是刚开始用，感觉一直不太适应，这个语法糖实在是有点甜。。。
