title: Java 8 进阶系列 - Stream API
date: 2020-03-14 16:15:47
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

&emsp;&emsp;Stream API是Java 8版本的又一大利器，借助于同样新出现的 Lambda 表达式，极大的提高编程效率和程序可读性。 尤其对于集合元素的遍历、过滤、数据汇聚等方面，在早版本的迭代器之外给另一种更优的方案。

&emsp;&emsp;Stream API提供串行和并行两种模式，串行模式与传统Iterator类似，数据按照一个方向流动，进行单向处理；而并发模式能够充分利用多核处理器的优势。 

&emsp;&emsp;Stream 的并行操作依赖于 Java7 中引入的 Fork/Join 框架（JSR166y）来拆分任务和加速处理过程。通常编写并行代码很难而且容易出错，但使用 Stream API 无需编写一行多线程的代码，就可以很方便地写出高性能的并发程序。

## （二）流的操作分类
### 1. Intermediate（中间操作）

 &emsp;&emsp; **Intermediate具有Lambda延迟性，不会立刻触发操作。** 一个流可以后面跟随零个或多个 intermediate 操作。其目的主要是打开流，做出某种程度的数据映射 / 过滤，然后返回一个新的流，交给下一个操作使用。这类操作都是惰性化的（lazy），就是说，仅仅调用到这类方法，并没有真正开始流的遍历。
 
  &emsp;&emsp; 中间操作又可以分为无状态的(Stateless)和有状态的(Stateful)，无状态的中间操作是指元素的处理不受前面元素的影响，而有状态的中间操作必须等到所有元素处理之后才知道最终结果，比如排序是有状态操作，在读取所有元素之前并不能确定排序结果，filter是无状态操作，符合过滤条件就被选出来，不关注其他元素的状态。
 

### 2. Terminal（终止操作）

 &emsp;&emsp; **Terminal触发整个流的操作。** 一个流只能有一个 terminal 操作，当这个操作执行后，流就被使用 “光” 了，无法再被操作。所以这必定是流的最后一个操作。Terminal 操作的执行，才会真正开始流的遍历，并且生成结果。
 
 &emsp;&emsp;终止操作又可以分为短路操作和非短路操作，短路操作是指不用处理全部元素就可以返回结果，比如找到第一个满足条件的元素。
    

<table class="table table-bordered table-hover table-valign-middle" style="font-size: 14px;text-align:center">
<thead>
<tr>
<th width="40%" colspan="2">操作类型</th>
<th width="60%">接口方法</th>
</tr>
</thead>
<tbody>
<tr>
<td rowspan="2" width="20%">Intermediate（中间操作）</td>
<td width="20%">无状态(Stateless)</td>
<td>
map(mapToInt、mapToLong、flatmap、mapToDouble、 flatMapToInt、flatMapToLong、flatMapToDouble)、unordered、filter、 peek、 parallel 、sequential  、isParallel
</td>
</tr>
<tr>
<td>有状态(Stateful)</td>
<td>distinct、 sorted、limit、 skip</td>
</tr>
<tr>
<td rowspan="2">Terminal（最终操作）</td>
<td>非短路操作</td>
<td>
forEach、forEachOrdered、 toArray、 reduce、collect、 max、min、count
</td>
</tr>
<tr>
<td>Short-circuiting（短路操作）</td>
<td>anyMatch、allMatch、noneMatch、findFirst、 findAny</td>
</tr>
</tbody>
</table>

    
# 二. 流的创建

## （一）常用创建方式

## 1. 独立的值
    Stream stream = Stream.of("a", "b", "c");

## 2. 数组
    String [] strArray = new String[] {"a", "b", "c"};
    stream = Stream.of(strArray);
    stream = Arrays.stream(strArray);
    
## 3. 集合
    List<String> list = Arrays.asList(strArray);
    stream = list.stream();

## （二）包装类型的构造

&emsp;&emsp;可以使用 Stream&lt;Integer&gt;、 Stream&lt;Double&gt;、 Stream&lt;Long&gt; ， 但是装箱、拆箱非常耗时，建议谨慎使用。
 
     IntStream ints = IntStream.of(1,2,3);
     LongStream longs = LongStream.of(4,5,6);
     DoubleStream doubles = DoubleStream.of(6,7,8);
     
     IntStream.range(1, 10).forEach(System.out::print);// [1,10) 区间
     IntStream.rangeClosed(1, 10).forEach(System.out::print);//[1,10] 区间

## （三）并行流的规则输出

&emsp;&emsp;parallel() 方法将普通流转换为并行流

    IntStream.range(1, 10).parallel().forEach(System.out::print); // 并行执行 效率高 但是输出结果不具备输入结果的有序性
    IntStream.range(1, 10).parallel().forEachOrdered(System.out::print);// 并行执行 效率高  严格要求输出结果按照输入结果顺序
    
## （四）流的转换

&emsp;&emsp;通过collect() 方法将流中的数据转成集合与值

### 1. 数组
    String[] strArray1 = stream.toArray(String[]::new);

### 2. 集合
    List<String> list1 = stream.collect(Collectors.toList());
    List<String> list2 = stream.collect(Collectors.toCollection(ArrayList::new));
    Set set1 = stream.collect(Collectors.toSet());
    Stack stack1 = stream.collect(Collectors.toCollection(Stack::new));
    
### 3. String
    String str = stream.collect(Collectors.joining()).toString();
    
    
# 三. 流操作

## （一）map映射

### 1. 一对一

    IntStream.of(1,2,3).map(x->x*2).forEach(System.out::println);
    
### 2. 合并流

    Stream<List<Integer>> inputStream = Stream.of(
                    Arrays.asList(1),
                    Arrays.asList(2,3),
                    Arrays.asList(4,5,6));
    Stream<Integer> child_stream = inputStream.flatMap(x->x.stream());
    
### 3. 合并流到其他类型 - 一对多

    DoubleStream doubleStream = inputStream.flatMapToDouble(x->x.stream().mapToDouble(Double::new));
    IntStream intStream = inputStream.flatMapToInt(x->x.stream().mapToInt(Integer::new));
    LongStream longStream = inputStream.flatMapToLong(x->x.stream().mapToLong(Long::new));
    
### 4. 转大小写、平方数等
    List<String> output = wordList.stream().map(String::toUpperCase).collect(Collectors.toList());
    List<Integer> nums = Arrays.asList(1, 2, 3, 4);
    List<Integer> squareNums = nums.stream().map(n -> n * n).collect(Collectors.toList()); 
    
## （二）filter 过滤器

    //留下偶数
    IntStream.range(1, 10).filter(x->(x&1)==0).forEach(System.out::println);
    
    Integer[] sixNums = {1, 2, 3, 4, 5, 6};
    Integer[] array = Stream.of(sixNums).filter(x->(x&1)==0).toArray(Integer[]::new);
    
    //  把单词挑出来
    List<String> output = reader.lines().flatMap(line -> Stream.of(line.split(REGEXP))).filter(word -> word.length() > 0).collect(Collectors.toList());

## （三）foreach peek(Intermediate) 输出

    Stream<List<Integer>> stream = Stream.of(Arrays.asList(1,2,3,4,5,6));
            
    stream.parallel().forEach(System.out::println);
    stream.parallel().forEachOrdered(System.out::println); //并行 强制有序
    // 体现了 访问者设计模式
    Stream.of("one", "two", "three", "four")
        .filter(e -> e.length() > 3)
        .peek(e -> System.out.println("Filtered value: " + e))
        .map(String::toUpperCase)
        .peek(e -> System.out.println("Mapped value: " + e))
        .collect(Collectors.toList());
        
        
输出结果： 
    
    Filtered value: three
    Mapped value: THREE
    Filtered value: four
    Mapped value: FOUR
    
## （四）findFirst、findAny
&emsp;&emsp;findAny、max/min、reduce 等方法返回Optional 非终结操作，可以对结果继续处理。 使用它的目的是尽可能避免 NullPointerException。 
    
    Integer[] sixNums = {1, 2, 3, 4, 5, 6};
    Stream.of(sixNums).findFirst().ifPresent(System.out::println);
                
    Stream.of(sixNums).findAny().ifPresent(System.out::println);
    // 返回的Optional 可以加上逻辑排除NPE
    Integer else1 = Stream.of(sixNums).filter(x->x<0).findAny().orElse(null);
              
&emsp;&emsp;findFirst、findAny找不到元素抛出NPE。可以加上Or系列方法返回默认值

## （五）reduce
&emsp;&emsp;这个方法的主要作用是把identity作为第二个参数BinaryOperator 函数的输入,执行操作后返回。

    // 字符串连接，concat = "ABCD"
    String concat = Stream.of("A", "B", "C", "D").reduce("", String::concat); 
    // 求最小值，minValue = -3.0
    double minValue = Stream.of(-1.5, 1.0, -3.0, -2.0).reduce(Double.MAX_VALUE, Double::min); 
    // 求和，sumValue = 10, 有起始值
    int sumValue = Stream.of(1, 2, 3, 4).reduce(0, Integer::sum);
    // 求和，sumValue = 10, 无起始值
    sumValue = Stream.of(1, 2, 3, 4).reduce(Integer::sum).get();
    // 过滤，字符串连接，concat = "ace"
    concat = Stream.of("a", "B", "c", "D", "e", "F").
     filter(x -> x.compareTo("Z") > 0).
     reduce("", String::concat);
     
&emsp;&emsp;以上 字符串拼接、数值的 sum、min、max、average 都是特殊的 reduce

## （六）limit/skip
&emsp;&emsp;limit返回Stream的前面n个元素；skip则是扔掉前n个元素

    List<String> personList = persons.stream().map(Person::getName).limit(10).skip(3).collect(Collectors.toList());
     

 **limit/skip ,放在sorted()后面并不能影响排序的次数**

&emsp;&emsp;有一种情况是 limit/skip 无法达到 short-circuiting 目的的，就是把它们放在 Stream 的排序操作后，原因跟 sorted 这个 intermediate 操作有关, 由于系统统并不知道 Stream 排序后的次序如何，所以 sorted 中的操作看上去就像完全没有被 limit 或者 skip 一样

 **并行流情况下不能使用limit() 将会影响并行操作的次序性能**

&emsp;&emsp;对一个 parallel 的 Steam 管道来说，如果其元素是有序的，那么 limit 操作的成本会比较大，因为它的返回对象必须是前 n 个也有一样次序的元素。取而代之的策略是取消元素间的次序，或者不要用 parallel Stream。

## （七）排序sorted

    List<Person> personList2 = persons.stream().limit(2).sorted((p1, p2) -> p1.getName().compareTo(p2.getName())).collect(Collectors.toList());
    
## （八）min/max/distinct

&emsp;&emsp;用 findFirst 来实现，但前者的性能会更好，为 O(n)，而 sorted 的成本是 O(n log n)。同时它们作为特殊的 reduce 方法被独立出来也是因为求最大最小值是很常见的操作。

    Stream<Integer> stream = Stream.generate(()->new Random().nextInt()).limit(100);
        
    long nums = 100-stream.distinct().count();
    Integer max = stream.max(Comparator.naturalOrder()).get();
    Integer min = stream.min(Comparator.naturalOrder()).get();
    
## （九）match
&emsp;&emsp;Stream有三个match方法，从语义上说：
    
 *  allMatch：Stream 中全部元素符合传入的 predicate，返回 true
 *  anyMatch：Stream 中只要有一个元素符合传入的 predicate，返回 true
 *  noneMatch：Stream 中没有一个元素符合传入的 predicate，返回 true
 
    
    List<Person> persons = new ArrayList();
    persons.add(new Person(1, "name" + 1, 10));
    persons.add(new Person(2, "name" + 2, 21));
    persons.add(new Person(3, "name" + 3, 34));
    persons.add(new Person(4, "name" + 4, 6));
    persons.add(new Person(5, "name" + 5, 55));
    boolean isAllAdult = persons.stream().
     allMatch(p -> p.getAge() > 18);
    System.out.println("All are adult? " + isAllAdult);
    boolean isThereAnyChild = persons.stream().
     anyMatch(p -> p.getAge() < 12);
    System.out.println("Any child? " + isThereAnyChild);
    
# 四. 流的生成

## （一）generate

&emsp;&emsp;通过Supplier接口，可以自己来控制流的生成。这种情形通常用于随机数、常量的 Stream，或者需要前后元素间维持着某种状态信息的 Stream。

&emsp;&emsp;generate内部维护一个无限制的循环，根据传入的规则生成数据，需要使用Limit限制数据生成的范围。

    Random seed = new Random();
    Supplier<Integer> random = seed::nextInt;
    Stream.generate(random).limit(10).forEach(System.out::println);
    //Another way
    IntStream.generate(() -> (int) (System.nanoTime() % 10000)).
    limit(10).forEach(System.out::println);

自定义Supplier
    
    Stream<Person> stream2 = Stream.generate(new Use_Max_Min_Distinct().new PersonSupplier()).limit(10);
            stream2.forEach(System.out::println);
      
    private class PersonSupplier implements Supplier<Person>{
        private Random random=new Random();
        @Override
        public Person get() {
            return new Person("Tom",random.nextInt());
        }
    }
    
    private class Person{
        private String name;
        // Constructor get set toString()
    }
    
## （二）iterate

&emsp;&emsp;iterate 跟 reduce 操作很像，接受一个种子值，和一个 UnaryOperator（例如 f）。

&emsp;&emsp;然后种子值成为 Stream 的第一个元素，f(seed) 为第二个，f(f(seed)) 第三个，以此类推。

    Stream.iterate(0, n -> n + 3).limit(10). forEach(x -> System.out.print(x + " "));
    // 0 3 6 9 12 15 18 21 24 27
    
# 五. 聚合操作

## （一）groupingBy

&emsp;&emsp;groupingBy是多值的聚合操作，参数是Function 

### 1.默认分组类型(List)

    Map<String, List<PersonVO>> teamMap = persons.stream()
            .collect(Collectors.groupingBy(PersonVO::getTeam));

### 2.指定分组类型

    Map<String, String> teamString = persons.stream()
                .collect(Collectors.toMap(PersonVO::getTeam, PersonVO::getName, (a,b)-> a+","+b));

### 3.分组求和

    Map<String, Integer> teamString = persons.stream()
            .collect(Collectors.groupingBy(PersonVO::getTeam, Collectors.summingInt(PersonVO::getAge)));

## （二）partitioningBy

&emsp;&emsp;partitioningBy是单值的操作，参数Predicate

        Map<Boolean, List<String>> partition = Stream.of("1", "2", "3", "4", "5", "6", "7")
                .collect(Collectors.partitioningBy(t->Integer.valueOf(t)>3));