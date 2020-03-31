title: Java 8 进阶系列 - Optional类深度解析
date: 2020-03-31 11:05:47
tags:
  - Java
categories:
  - 技术
comments: true
original: true

---
@[toc]

# 一. 前言

## （一）概述

&emsp;&emsp;在Java8之前，null值的判断一直是困扰Java程序员的问题，大家可能都有这样的经历：调用一个方法得到了返回值却不能直接将返回值作为参数去调用别的方法。我们首先要判断这个返回值是否为null，只有在非空的前提下才能将其作为其他方法的参数。为了防止NullPointException的出现，我们不得不编写大量的防御性代码，如下例所示：

    Passenger passenger = SomeMehtod.getPassenger();
    if(passenger != null && passenger.getCert() != null 
       && passenger.getCert().getPersonalInfo != null){   
        return passenger.getCert().getPersonalInfo().getName();
    }
    else return "default name";

&emsp;&emsp;对于嵌套比较深的类，上述代码太常见了，大段的&&条件判断非常容易出错，同时代码可读性也非常差。

&emsp;&emsp;Java 8引入了新的Optional类，专门用来解决null值问题，对于嵌套类的空判断，使用Optional比传统的层层剥皮判断要更加优雅，代码量也会少很多。然而需要特别说明的是，目前Optional的最优场景还是建议配合Stream API使用。如果不结合Stream API来看，虽然也能改造相当一部分if else冗余嵌套的场景，但Optional也可能会让代码变得更冗余，过犹不及，需要结合具体场景具体分析。

&emsp;&emsp;本文会逐个探讨Optional类包含的方法，并通过一两个示例展示如何使用。

## （二）方法及示例
### 1. of

> 为非null的值创建一个Optional。

&emsp;&emsp; of方法通过工厂方法创建Optional类。需要注意的是，创建对象时传入的参数不能为null。如果传入参数为null，则抛出NullPointerException。
 
    //调用工厂方法创建Optional实例
    Optional<String> name = Optional.of("Sanaulla");
    //传入参数为null，抛出NullPointerException.
    Optional<String> someNull = Optional.of(null);

### 2. ofNullable

> 为指定的值创建一个Optional，如果指定的值为null，则返回一个空的Optional。

&emsp;&emsp;ofNullable与of方法相似，唯一的区别是可以接受参数为null的情况。示例如下：
    
    //下面创建了一个不包含任何值的Optional实例
    //例如，值为'null'
    Optional empty = Optional.ofNullable(null);
    
### 3. isPresent

> 非常容易理解，如果值存在返回true，否则返回false。

    //isPresent方法用来检查Optional实例中是否包含值
    if (name.isPresent()) {
      //在Optional实例内调用get()返回已存在的值
      System.out.println(name.get());//输出Sanaulla
    }
    
### 4. get

> 如果Optional有值则将其返回，否则抛出NoSuchElementException。

&emsp;&emsp;上面的示例中，get方法用来得到Optional实例中的值。下面我们看一个抛出NoSuchElementException的例子：

    //执行下面的代码会输出：No value present 
    try {
      //在空的Optional实例上调用get()，抛出NoSuchElementException
      System.out.println(empty.get());
    } catch (NoSuchElementException ex) {
      System.out.println(ex.getMessage());
    }
    
### 5. ifPresent

> 如果Optional实例有值则为其调用consumer，否则不做处理。

&emsp;&emsp;要理解ifPresent方法，首先需要了解Consumer类。简答地说，Consumer类包含一个抽象方法。该抽象方法对传入的值进行处理，但没有返回值。Java8支持不用接口直接通过lambda表达式传入参数。
&emsp;&emsp;如果Optional实例有值，调用ifPresent()可以接受接口段或lambda表达式。类似下面的代码：

    //ifPresent方法接受lambda表达式作为参数。
    //lambda表达式对Optional的值调用consumer进行处理。
    name.ifPresent((value) -> {
      System.out.println("The length of the value is: " + value.length());
    });
    
### 6. orElse

> 如果有值则将其返回，否则返回指定的其它值。
    
&emsp;&emsp;如果Optional实例有值则将其返回，否则返回orElse方法传入的参数。示例如下：

    //如果值不为null，orElse方法返回Optional实例的值。 如果为null，返回传入的消息。
    System.out.println(empty.orElse("There is no value present!"));
    //输出：There is no value present!
    System.out.println(name.orElse("There is some value!"));
    //输出：Sanaulla

### 7. orElseGet

> 如果有值则将其返回，否则返回指定的其它值。
    
&emsp;&emsp;orElseGet与orElse方法类似，区别在于得到的默认值。orElse方法将传入的字符串作为默认值，orElseGet方法可以接受Supplier接口的实现用来生成默认值。示例如下：

    //orElseGet与orElse方法类似，区别在于orElse传入的是默认值，
    //orElseGet可以接受一个lambda表达式生成默认值。
    //输出：Default Value
    System.out.println(empty.orElseGet(() -> "Default Value"));
    //输出：Sanaulla
    System.out.println(name.orElseGet(() -> "Default Value"));
    
### 8. orElseThrow

> 如果有值则将其返回，否则抛出supplier接口创建的异常。
    
&emsp;&emsp;在orElseGet方法中，我们传入一个Supplier接口。然而，在orElseThrow中我们可以传入一个lambda表达式或方法，如果值不存在来抛出异常。示例如下：

    try {
      //orElseThrow与orElse方法类似。与返回默认值不同，
      //orElseThrow会抛出lambda表达式或方法生成的异常 
    
      empty.orElseThrow(ValueAbsentException::new);
    } catch (Throwable ex) {
      //输出: No value present in the Optional instance
      System.out.println(ex.getMessage());
    }
    
### 9. map

&emsp;&emsp;map方法用来对Optional实例的值执行一系列操作。通过一组实现了Function接口的lambda表达式传入操作。

    //map方法执行传入的lambda表达式参数对Optional实例的值进行修改。
    //为lambda表达式的返回值创建新的Optional实例作为map方法的返回值。
    Optional<String> upperName = name.map((value) -> value.toUpperCase());
    System.out.println(upperName.orElse("No value found"));
    
### 10. flatMap

&emsp;&emsp;flatMap方法与map方法类似，区别在于mapping函数的返回值不同。map方法的mapping函数返回值可以是任何类型T，而flatMap方法的mapping函数必须是Optional。

    //map方法中的lambda表达式返回值可以是任意类型，在map函数返回之前会包装为Optional。 
    //但flatMap方法中的lambda表达式返回值必须是Optionl实例。 
    upperName = name.flatMap((value) -> Optional.of(value.toUpperCase()));
    System.out.println(upperName.orElse("No value found"));//输出SANAULLA
    
### 11. filter

&emsp;&emsp;filter个方法通过传入限定条件对Optional实例的值进行过滤。

> 如果有值并且满足断言条件返回包含该值的Optional，否则返回空Optional。

    //filter方法检查给定的Option值是否满足某些条件。
    //如果满足则返回同一个Option实例，否则返回空Optional。
    Optional<String> longName = name.filter((value) -> value.length() > 6);
    System.out.println(longName.orElse("The name is less than 6 characters"));//输出Sanaulla
    
    //另一个例子是Optional值不满足filter指定的条件。
    Optional<String> anotherName = Optional.of("Sana");
    Optional<String> shortName = anotherName.filter((value) -> value.length() > 6);
    //输出：name长度不足6字符
    System.out.println(shortName.orElse("The name is less than 6 characters"));
    
    