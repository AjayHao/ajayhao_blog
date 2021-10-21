title: Java 8 进阶系列 - Lambda 表达式
date: 2020-03-07 22:15:47
tags:
  - Java
categories:
  - 技术
comments: true
original: true

---
[toc]

# 一. 前言

&emsp;&emsp;Lambda表达式是Java 8最重要的一个新特性，它允许我们将行为传到函数里。在Java 8之前，如果想将行为传入函数，仅有的选择就是匿名类，匿名类的写法代码冗长且需要遵循特定的代码模板。Lambda表达式取代了匿名类，取消了模板，允许用函数式风格编写代码；使得代码精简的同时，可读性更好，表达更清晰。
&emsp;&emsp;下面我们来看几个最佳实践
    
# 二. 最佳实践

## 1. 简化内部类的写法
&emsp;&emsp;熟悉lambda的最快方法是使用lambda表达式替换匿名类，比如构造线程时的Runnable接口

    // Java 8之前：
    new Thread(new Runnable() {
        @Override
        public void run() {
        System.out.println("Before Java8, too much code for too little to do");
        }
    }).start();
    
[//]:#(Markdown注释)
    
    //Java 8方式：
    new Thread( () -> System.out.println("In Java8, Lambda expression rocks !!") ).start();

## 2. 集合类的迭代
&emsp;&emsp;针对集合类，最常见的操作就是进行循环，并将业务逻辑应用于各个元素。使用Java 8的Lambda表达式对集合做迭代操作简便很多。
    
    // Java 8之前：
    List features = Arrays.asList("Lambdas", "Default Method", "Stream API", "Date and Time API");
    for (String feature : features) {
        System.out.println(feature);
    }
    
[//]:#(Markdown注释)

    // Java 8之后：
    List features = Arrays.asList("Lambdas", "Default Method", "Stream API", "Date and Time API");
    features.forEach(n -> System.out.println(n));
    // 使用Java 8的方法引用更方便，方法引用由::双冒号操作符标示，
    features.forEach(System.out::println);

输出结果：
    
    Lambdas
    Default Method
    Stream API
    Date and Time API
    
## 3. 支持函数编程
&emsp;&emsp;为了支持函数编程，Java 8加入了一个新的包java.util.function，其中有一个接口java.util.function.Predicate是支持Lambda函数编程：
    
    public static void filter(List names, Predicate condition) {
      names.stream().filter((name) -> (condition.test(name)))
        .forEach((name) -> {System.out.println(name + " ");
      });
    }

    public static void main(args[]){
      List languages = Arrays.asList("Java", "Scala", "C++", "Haskell", "Lisp");
    
      System.out.println("Languages which starts with J :");
      filter(languages, (str)->str.startsWith("J"));
    
      System.out.println("Languages which ends with a ");
      filter(languages, (str)->str.endsWith("a"));
    
      System.out.println("Print all languages :");
      filter(languages, (str)->true);
    
       System.out.println("Print no language : ");
       filter(languages, (str)->false);
    
       System.out.println("Print language whose length greater than 4:");
       filter(languages, (str)->str.length() > 4);
    }
    
输出结果：

    Output:
    Languages which starts with J :
    Java
    Languages which ends with a
    Java
    Scala
    Print all languages :
    Java
    Scala
    C++
    Haskell
    Lisp
    Print no language :
    Print language whose length greater than 4:
    Scala
    Haskell
    
## 4. 管道方式的处理
&emsp;&emsp;Java 8里面新增的Stream API ，让集合中的数据处理起来更加方便，性能更高，可读性更好。

&emsp;&emsp;假设一个业务场景：对于20元以上的商品，进行9折处理，最后得到这些商品的折后价格。

    final BigDecimal totalOfDiscountedPrices = prices.stream()
    .filter(price -> price.compareTo(BigDecimal.valueOf(20)) > 0)
    .map(price -> price.multiply(BigDecimal.valueOf(0.9)))
    .reduce(BigDecimal.ZERO,BigDecimal::add);
    
    System.out.println("Total of discounted prices: " + totalOfDiscountedPrices);
    
&emsp;&emsp;如上例所示，用面向对象处理这些数据，需要多少行、循环，需要声明多少个中间变量。而使用lambda表达式后，可以以极其简洁的代码量完成。