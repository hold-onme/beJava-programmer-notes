## JDK8中新日期时间API

**新日期时间出现的背景**

日期时间API的迭代：
第一代：jdk 1.0 Date类
第二代：jdk 1.1 Calendar类，一定程度上替换Date类
第三代：jdk 1.8 提出了新的一套API

**前两代存在的问题举例：**
可变性：像日期和时间这样的类应该是不可变的。
偏移性：Date中的年份是从1900开始的，而月份都从0开始。
格式化：格式化只对Date用，Calendar则不行。
此外，它们也不是线程安全的；不能处理闰秒等。



**新的日期时间API在	java.time	包下**

- java.time.LocalDateTime
- java.time.LocalDate
- java.time.LocalTime

**说明：**

1. LocalDateTime相较于LocalDate、LocalTime，使用频率要高
2. 类似于Calendar

**常用方法介绍**

```java
//now():获取当前的日期、时间、日期+时间
LocalDate localDate = LocalDate.now();
LocalTime localTime = LocalTime.now();
LocalDateTime localDateTime = LocalDateTime.now();

System.out.println(localDate);
System.out.println(localTime);
System.out.println(localDateTime);

//of():设置指定的年、月、日、时、分、秒。没有偏移量
LocalDateTime localDateTime1 = LocalDateTime.of(2020, 10, 6, 13, 23, 43);
System.out.println(localDateTime1);


//getXxx()：获取相关的属性
System.out.println(localDateTime.getDayOfMonth());
System.out.println(localDateTime.getDayOfWeek());
System.out.println(localDateTime.getMonth());
System.out.println(localDateTime.getMonthValue());
System.out.println(localDateTime.getMinute());

//体现不可变性
//withXxx():设置相关的属性
LocalDate localDate1 = localDate.withDayOfMonth(22);
System.out.println(localDate);
System.out.println(localDate1);


LocalDateTime localDateTime2 = localDateTime.withHour(4);
System.out.println(localDateTime);
System.out.println(localDateTime2);

//不可变性
LocalDateTime localDateTime3 = localDateTime.plusMonths(3);
System.out.println(localDateTime);
System.out.println(localDateTime3);

LocalDateTime localDateTime4 = localDateTime.minusDays(6);
System.out.println(localDateTime);
System.out.println(localDateTime4);
```





- java.time.Instant

**说明：**

​	Instant的使用类似于 java.util.Date类

```java
//now():获取本初子午线对应的标准时间
Instant instant = Instant.now();
System.out.println(instant);//2019-02-18T07:29:41.719Z

//添加时间的偏移量
OffsetDateTime offsetDateTime = instant.atOffset(ZoneOffset.ofHours(8));
System.out.println(offsetDateTime);//2019-02-18T15:32:50.611+08:00

//toEpochMilli():获取自1970年1月1日0时0分0秒（UTC）开始的毫秒数  ---> Date类的getTime()
long milli = instant.toEpochMilli();
System.out.println(milli);

//ofEpochMilli():通过给定的毫秒数，获取Instant实例  -->Date(long millis)
Instant instant1 = Instant.ofEpochMilli(1550475314878L);
System.out.println(instant1);
```



- java.time.format.DateTimeFormatter

**说明：**DateTimeFormatter:格式化或解析日期、时间类似于SimpleDateFormat