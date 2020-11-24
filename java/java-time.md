# Instant

## 简介

+ java.time 包
+ java 8 关于日期时间的新特性
+ 精确到纳秒
+ 实际上是使用两个Long型存储（精确到纳秒后，一个Long型不够）
+ 起始时间点为Unix元年初始单位（1970-01-01  零点）

## 获取

+ 获取当前时间

  ```java
  Instant instant= Instant.now();
  ```

+ Date

  ```java
  Instant instant = Instant.ofEpochMilli(new Date().getTime()); 
  ```

+ 字符串

  + Instant本身是一个标准时间点，不包含时区信息，因此字符串必须为UTC格式

  ```java
  Instant instant = Instant.parse("1995-10-23T10:12:35Z");
  ```

+ LocalDateTime 

  + ZoneOffset 用以指定时间偏移量，即跨时区情况下的时差，可用不同方法指定（示例为int值）

  ```java
  LocalDateTime localDateTime = LocalDateTime.now();
  Instant instant = localDateTime.toInstant(ZoneOffset.ofHours(8));
  ```

+ Calendar

  ```java
  Calendar calendar = Calendar.getInstance();
  Instant instant = calendar.toInstant();
  ```

  

## 操作



+ 检查 是否支持某一时间单位的检查方法isSupported()

  + 该方法提供了两种类型TemporalField 和 TemporalUnit，两种类型的参数覆盖的单位不同

  ```java
  Instant instant = Instant.parse("1995-10-23T10:12:35Z");
  boolean value;
  value = instantChar.isSupported(ChronoField.MILLI_OF_SECOND);
  value = instantChar.isSupported(ChronoUnit.DAYS);
  ```


+ 支持指定单位运算，如plusSeconds等

+ 指定时间单位计算时间差

  ```java
  Instant now = Instant.now();
  Instant resultInstant;
  // 加操作
  resultInstant = now.plus(Duration.ofHours(1).plusMinutes(1));
  // 减操作
  resultInstant = now.minus(Duration.ofHours(1).plusMinutes(1));
  // 5天前
  resultInstant = now.minus(5, ChronoUnit.DAYS);
  // 30分钟后
  resultInstant = now.plus(Duration.ofMinutes(30));
  // 相差分钟数
  Long diffAsMinutes = ChronoUnit.MINUTES.between(now, resultInstant);
  ```

+ 时间比较

  ```java
  ↑ ↓
  // compareTo方法比较
  resultInstant.compareTo(now);
  // 判断日期先后，返回类型为布尔
  resultInstant.isBefore(now);
  ```

+ 指定单位进行截断操作

  + 注意：截断后的时间，单位小于指定单位的归零

  ```java
  Instant now = Instant.now();
  Instant resultInstant;
  // 此时为当前月的0时刻
  resultInstant = instant.truncatedTo(ChronoUnit.MONTHS);
  ```

  

# Date

## 简介

+ java.util
+ 封装日期和时间
+ 精确到毫秒
+ long 型构造也是从Unix元年开始

## 获取

+ 直接获取当前时间，通过 System.currentTimeMillis() 构造

  ```java
  Date date = new Date();
  ```

+ long型构造

  ```java
  // Fri Nov 20 13:43:41 CST 2020
  long millisec = 1605851021164L;
  Date date = new Date(millisec);
  ```

+ Instant

  ```java
  Date date = Date.from(Instant.now());
  ```

+ LocalDateTime 

  + 本质上是通过Instant转换

  ```java
  Date date = Date.from(LocalDateTime.now().atZone(ZoneId.systemDefault()).toInstant());
  ```

+ Calendar

  ```java
  Calendar cal = Calendar.getInstance();
  Date date2 = cal.getTime();
  ```

+ String

  + SimpleDateFormat （线程不安全）类提供了parse和format 方法，其中
    + parse：字符串转换为Date
    + format：按指定规则格式化，其中M代表月份，且必须大写

  ```java
  SimpleDateFormat simpleDateFormat = new SimpleDateFormat("yyyy-MM-dd");
  Date date = simpleDateFormat.parse("2020-11-20");
  System.out.println(date);
  System.out.println(simpleDateFormat.format(date));
  ```

  

## 操作

+ Date的getTime() 方法返回long型时间戳
+ Date类型的时间操作使用Calendar进行

+ 比较时间大小before/after



# LocalDateTime

## 简介

+ java.time
+ 包含年月日时分秒信息，所以可以随时处理为LoaclDate 和LocalTime 使用
+ 对日期时间 的操作基本和 LoaclDate 和LocalTime 一致，但都只能处理相应单位

## 获取

+ 获取当前时间

  ```java
  LocalDateTime localDateTime = LocalDateTime.now();
  ```

  + 获取date

    ```java
    LocalDate localDate = localDateTime.toLocalDate();
    ```

  + 获取time

    ```java
    LocalTime localTime = localDateTime.toLocalTime();
    ```

+ Date （本质上也是Instant转换）

  ```java
  Date dateTest = new Date();
  LocalDateTime localDateTime = LocalDateTime.ofInstant(dateTest.toInstant(), ZoneId.systemDefault());
  LocalDateTime localDateTime1 = Instant.ofEpochMilli(dateTest.getTime()).atZone(ZoneId.systemDefault()).toLocalDateTime();
  ```

+ String

  ```java
  DateTimeFormatter df = DateTimeFormatter.ofPattern("yyyy-MM-dd HH:mm:ss");
  LocalDateTime time = LocalDateTime.now();
  String localTime = df.format(time);
  LocalDateTime ldt = LocalDateTime.parse("2020-11-23 10:47:05",df);
  System.out.println("LocalDateTime转成String类型的时间："+localTime);
  System.out.println("String类型的时间转成LocalDateTime："+ldt);
  ```

## 操作

+ LocalDateTime 在操作方面非常灵活，日期时间的加减操作大致分为两类

  ```javascript
  LocalDateTime now = LocalDateTime.now();
  LocalDate localDate = LocalDate.of(2023, 9, 10);
  LocalDateTime otherTime = localDate.atTime(LocalTime.now());
  // 两种类型实现效果均为当前时间加两天（减操作同理）
  LocalDateTime localDateTime = LocalDateTime.now().plusDays(2);
  localDateTime = localDateTime.plus(2, ChronoUnit.DAYS);
  ```

+ 日期时间的获取

  + 提供多个方法，大致分为指定方法和指定类型两种

  ```java
  ↑ ↓
  // 本月第一天
  LocalDateTime firstDayOfThisMonth = now.with(TemporalAdjusters.firstDayOfMonth());
  // 栗子：获取星期  当前时间   2020-11-23T10:47:44.313
  DayOfWeek dayOfWeek = localDateTime.getDayOfWeek();
  int dayOfWeek1 = now.get(ChronoField.DAY_OF_WEEK);
  // 打印结果  Monday  1   同理  getDayOfYear()获取当前日期是今年的第多少天（330）
  System.out.println(dayOfWeek);
  System.out.println(dayOfWeek1);
  ```

+ 在日期比较方面，多了一个isEqual，用以比较两个时间或日期，返回一个布尔

  ```java
  ↑ ↓
  now.isEqual(localDate);
  now.isBefore(localDate);
  now.isAfter(localDate);
  ```

+ 其他方法

  ```java
  ↑ ↓
  // 时间单位 当前LocalDateTime 输出结果会是Nanos
  System.out.println(now.query(TemporalQueries.precision()));
  // 允许范围，示例为 DAY_OF_WEEK 则输出 1-7
  System.out.println(now.range(ChronoField.DAY_OF_WEEK));
  // 时间差  示例为两个时间的天数差
  System.out.println(now.until(otherTime,ChronoUnit.DAYS));
  ```

  

# Calendar

