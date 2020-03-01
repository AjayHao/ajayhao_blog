title: Java 8 进阶系列 - 新日期/时间库
date: 2020-02-29 14:17:03
tags:
  - Java
categories:
  - 技术
comments: true
original: true

---
@[toc]

# 一. 前言

&emsp;&emsp;Java的老版本中，日期及时间的处理一直以来都饱受诟病，原生API中缺少对于日期的计算、调整的良好支持，需要通过日历工具类（java.util.Calendar，下文简称Calendar类）进行辅助，而日期的格式化与转换需要通过SimpleDateFormat类进行处理。而Java 8之前的迷之设计，例如将java.util.Date（下文简称为Date类）设计为可修改的，以及将Calendar类及SimpleDateFormat类设计成非线程安全的，都使得日期操作在高并发场景中需要格外小心。 

&emsp;&emsp;Java 8引入了一套全新的时间日期库，最大的优点就在于它定义清楚了时间日期相关的一些概念，比方说，瞬时时间（Instant）,持续时间（duration），日期（date）,时间（time），时区（time-zone）以及时间段（Period）。同时它也借鉴了Joda库的一些优点，比如将人和机器对时间日期的理解区分开的。Java 8仍然延用了ISO的日历体系，并且与它的前辈们不同，java.time包中的类是不可变且线程安全的。新的时间及日期API位于java.time包中，下面是里面的一些关键的类：

  * Instant——它代表的是时间戳
  * LocalDate——不包含具体时间的日期，比如2014-01-14。它可以用来存储生日，周年纪念日，入职日期等。
  * LocalTime——它代表的是不含日期的时间
  * LocalDateTime——它包含了日期及时间，不过还是没有偏移信息或者说时区。
  * ZonedDateTime——这是一个包含时区的完整的日期时间，偏移量是以UTC/格林威治时间为基准的。
  
&emsp;&emsp;新的库还增加了ZoneOffset及ZoneId，可以为时区提供更好的支持。另外，使用了新的格式化类DateTimeFormatter之后，日期的解析及格式化也变得焕然一新了。新赠的Year对象，Month对象也可以更好的用来处理年月的推算和展示，下面我们通过一些实例来瞧一瞧。

# 二. 场景实例
## （一）. 向上兼容
&emsp;&emsp;大量项目中现存的日期工具类都会使用Date类型作为出入参对象，切换新接口面临最大的问题是接口参数的适配，因此第一步我们先看下**Date类型与新日期库类型的转换**。基于此，后续的所有场景就可以用新日期库进行处理了。

### Instant对象与Date对象互转
    // Instant对象转Date
    private static Date from(Instant instant) {
        return new Date(instant.toEpochMilli());
    }

    // 时间戳转Instant
    private static Instant toInstant(Long timeMilli) {
        Date d = new Date(timeMilli);
        return d.toInstant();
    }

## （二）. 日期、时间对象转换
&emsp;&emsp;有了Instant对象后，与LocalDate及LocalDateTime对象的转换就方便多了

### Instant对象转LocalDateTime对象
    Instant instant = new Date().toInstant();
    LocalDateTime localDateTime = LocalDateTime.ofInstant(instant, ZoneId.systemDefault());
    System.out.println("Local datetime : " + localDateTime);
    
    //打印结果
    Local datetime : 2020-02-23T21:20:14.256

### LocalDateTime对象转Instant对象
    LocalDateTime localDateTime = LocalDateTime.now();
    Instant instant = localDateTime.atZone(ZoneId.systemDefault()).toInstant();
    System.out.println("instant : " + instant);
    
    //打印结果
    instant : 2020-02-23T21:30:00Z

### LocalDateTime对象转LocalDate对象
    LocalDateTime localDateTime = LocalDateTime.now();
    LocalDate localDate = localDateTime.toLocalDate();
    System.out.println("Local date : " + localDate);
    
    //打印结果
    Local date : 2020-02-23

### LocalDateTime对象转LocalTime对象
    LocalDateTime localDateTime = LocalDateTime.now();
    LocalTime localTime = localDateTime.toLocalTime();
    System.out.println("Local time : " + localTime);
    
    //打印结果
    Local time : 21:30


## （三）. 日期、时间获取
### 当前日期
    LocalDate today = LocalDate.now();
    System.out.println("Local date : " + today);

    //打印结果
    Local date : 2020-02-23   
    
### 当前时间
    LocalTime now = LocalTime.now();
    System.out.println("Local time : " + now);
    
    //打印结果
    Local time : 21:20:14.256

### 当前日期时间
    LocalDateTime now = LocalDateTime.now();
    // 或者 Instant now = Instant.now();
    System.out.println("Local datetime : " + now);
    
    //打印结果
    Local datetime : 2020-02-23T21:20:14.256
    
### 所在年、月、日
    LocalDate currentDate = LocalDate.now();
    System.out.println("所在年:"+currentDate.getYear());
    System.out.println("所在月:"+currentDate.getMonthValue());
    System.out.println("所在日:"+currentDate.getDayOfMonth());

    //打印结果
    所在年:2020
    所在月:2
    所在日:23

### Long类型的10位秒数、13位毫秒数
    Instant now = Instant.now();
    System.out.println("秒数:"+now.getEpochSecond());
    System.out.println("毫秒数:"+now.toEpochMilli());
    
    //打印结果
    秒数:1582883565
    毫秒数:1582883565623
    
### 根据年月日的值获取LocalDate、LocalDateTime
    LocalDate.of(2020, 2, 23);     // 2020-02-23
    LocalDateTime.of(2020, 2, 23, 10, 30, 0); // 2020-02-23T10:30
    
### 根据某年的第n天获取LocalDate
    LocalDate.ofYearDay(2020, 60);  // 2020-02-29

### 常用日期时间获取场景
    LocalDate.now().getYear(); // 年份  
    LocalDate.now().getMonthValue(); // 月份（数值表示， 从1开始）  
    LocalDate.now().getMonth(); // 月份（英文[enum]表示）  
    LocalDate.now().getDayOfMonth(); // 日期（从1开始）  
    LocalDate.now().getDayOfYear(): // 当天所在这一年的第几天（从1开始）  
    LocalDate.now().getDayOfWeek(); // 星期几  
    LocalDate.now().lengthOfYear(); // 当年的天数  
    LocalDate.now().lengthOfMonth(); //当月的天数

## （四）. 日期推算
&emsp;&emsp;此处列示一些常用的场景，实际需求中大多数的场景可通过这些案例灵活组合来搞定。

### 加法
    LocalDate localDate = LocalDate.of(2020, 2, 23);
    localDate.plusDays(1);       // 加1天：2020-02-24
    localDate.plusWeeks(1);      // 加1周：2020-03-01
    localDate.plusMonths(1);     // 加1月：2020-03-23
    localDate.plusYears(1);      // 加1年：2021-02-23
    
### 减法
    LocalDate localDate = LocalDate.of(2020, 2, 23);
    localDate.minusDays(1);       // 减1天：2020-02-22
    localDate.minusWeeks(1);      // 减1周：2020-02-16
    localDate.minusMonths(1);     // 减1月：2020-01-23
    localDate.minusYears(1);      // 减1年：2019-02-23
&emsp;&emsp;加法方法传入负数可同样实现减法效果 
    
### 普通日期转换
&emsp;&emsp;这边的转换指时间日期按照规则计算并返回，并不影响调用对象localDate的原始值
    
    LocalDate localDate = LocalDate.of(2020, 2, 23);
    localDate.withDayOfMonth(1));  // 转为该月的第1天： 2020-02-01
    localDate.withDayOfYear(1));   // 转为该年的第1天： 2020-01-01
    localDate.withMonth(1));       // 替换月份为1：2020-01-23          localDate.withYear(2019));     // 替换年份为2019：2019-02-23
    
### 高级日期转换
&emsp;&emsp;高级日期替换需要配合TemporalAdjusters这个类，其提供了自由度更高的替换
    ![3svQdU.png](https://s2.ax1x.com/2020/02/29/3svQdU.png)
    
&emsp;&emsp;具体用法如下：
    
    LocalDate localDate = LocalDate.of(2020, 2, 23);
    localDate.with(TemporalAdjusters.lastDayOfMonth()));      // 该月最后一天：2020-02-29
    localDate.with(TemporalAdjusters.firstDayOfNextMonth())); // 下月第一天：2020-03-01
    localDate.with(TemporalAdjusters.lastDayOfYear()));       // 该年最后一天：2020-12-31

&emsp;&emsp;LocalDate的getMonth方法和getDayOfWeek可以分别返回Month和DayOfWeek对象，也有些较实用的方法，比如拿到本季度的第一个月

    localDate.getMonth().firstMonthOfQuarter()
 
 虽然返回的也是Month类型，可以通过简单转换提取日期，比如返回所在季度第一天
 
    LocalDate localDate = LocalDate.of(2020, 2, 23);
    LocalDate.of(localDate.getYear(), localDate.getMonth().firstMonthOfQuarter().getValue(),1) // 该年最后一天：2020-01-01
    
### 日期的比较
&emsp;&emsp;可以对两个 LocalDate 进行比较，可以判断一个日期是否在另一个日期之前或之后，或者判断两个日期是否是同年同月同日。

    LocalDate localDate = LocalDate.of(2020, 2, 23);
    System.out.println("是否在当天之前：" + localDate.isBefore(LocalDate.now()));
    System.out.println("是否在当天之后：" + localDate.isAfter(LocalDate.now()));
    System.out.println("是否在当天：" + localDate.isEqual(LocalDate.now()));
    System.out.println("今年是否是闰年：" + LocalDate.now().isLeapYear());

    //是否在当天之前：true
    //是否在当天之后：false
    //是否在当天：false
    //今年是否是闰年：true
      
## （五）. 日期、时间格式化
&emsp;&emsp;LocalDate的默认输出格式(即toString())为 yyyy-MM-dd

&emsp;&emsp;LocalTime因分钟以下单位的0不自动显示，因此默认输出格式(即toString())为在 HH:mm到HH:mm:ss.SSSSSSSSS之间

&emsp;&emsp;LocalDateTime的默认输出格式则为两者的组合，以T拼接

&emsp;&emsp;使用旧的Date对象时，我们用SimpleDateFormat进行格式化显示。使用新的LocalDateTime或ZonedLocalDateTime时，我们要进行格式化显示，就要使用DateTimeFormatter。
            
&emsp;&emsp;和SimpleDateFormat不同的是，DateTimeFormatter不但是不变对象，它还是线程安全的。线程的概念我们会在后面涉及到。现在我们只需要记住：因为SimpleDateFormat不是线程安全的，使用的时候，只能在方法内部创建新的局部变量。而DateTimeFormatter可以只创建一个实例，到处引用。
            
创建DateTimeFormatter时，我们仍然通过传入格式化字符串实现：
    
    DateTimeFormatter formatter = DateTimeFormatter.ofPattern("yyyy-MM-dd HH:mm");
    
### 指定格式输出
    LocalDateTime localDateTime = LocalDateTime.of(2020, 2, 23, 10, 30, 0);
    DateTimeFormatter formatter = DateTimeFormatter.ofPattern("yyyy-MM-dd HH:mm");
    System.out.println(formatter.format(localDateTime));
    
    // 2020-02-23 10:30
    
### 指定格式输入
&emsp;&emsp;构造指定格式的日期格式对象，是将formatter传给相应日期时间对象的parse方法
    
    String startDateStr = "20200223";
    DateTimeFormatter dateFormatter = DateTimeFormatter.ofPattern("yyyyMMdd");
    LocalDate startDate = LocalDate.parse(startDateStr, dateFormatter);
    System.out.println(startDate);
    // 2020-02-23

    String startTimeStr = "2020-02-23 10:30:00:222";
    DateTimeFormatter timeFormatter = DateTimeFormatter.ofPattern("yyyy-MM-dd HH:mm:ss:SSS");
    LocalDateTime startDateTime = LocalDateTime.parse(startTimeStr, timeFormatter);
    System.out.println(startDateTime);
    // 2020-02-23T10:30:00.222

### 月份、星期中文输出
&emsp;&emsp;LocalDate的getMonth方法和getDayOfWeek可以分别返回Month和DayOfWeek对象。这两个对象也有些很实用的方法，比如可以指定语言输出的月份与星期
    
    LocalDate localDate = LocalDate.of(2020, 2, 23);
    localDate.getMonth().getDisplayName(TextStyle.FULL, Locale.CHINA);  // 二月
    localDate.getDayOfWeek().getDisplayName(TextStyle.FULL, Locale.CHINA);  // 星期日

# 三. 总结

&emsp;&emsp;Java 8日期/时间API是JSR-310的实现，它的实现目标是克服旧的日期时间实现中所有的缺陷，新的日期/时间API的一些设计原则是：
  * 不变性：新的日期/时间API中，所有的类都是不可变的，这对多线程环境有好处。
  * 关注点分离：新的API将人可读的日期时间和机器时间（unix timestamp）明确分离，它为日期（Date）、时间（Time）、日期时间（DateTime）、时间戳（unix timestamp）以及时区定义了不同的类。
  * 清晰：在所有的类中，方法都被明确定义用以完成相同的行为。举个例子，要拿到当前实例我们可以使用now()方法，在所有的类中都定义了format()和parse()方法，而不是像以前那样专门有一个独立的类。为了更好的处理问题，所有的类都使用了工厂模式和策略模式，一旦你使用了其中某个类的方法，与其他类协同工作并不困难。
  * 实用操作：所有新的日期/时间API类都实现了一系列方法用以完成通用的任务，如：加、减、格式化、解析、从日期/时间中提取单独部分，等等。
  * 可扩展性：新的日期/时间API是工作在ISO-8601日历系统上的，但我们也可以将其应用在非ISO的日历上。
  
&emsp;&emsp;因此新日期/时间API才成为是除Java核心API以外另一项倍受欢迎的内容。