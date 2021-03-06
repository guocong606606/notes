

# stream

+ 类

```java
@Data
@Accessors(chain = true)
public class User {

    @ApiModelProperty("主键")
    private Integer id;

    @ApiModelProperty("年龄")
    private Integer age;

    @ApiModelProperty("姓名")
    private String name;
    
    @ApiModelProperty("性别，1男；2女")
    private String sex;
}
```

```java

List<User> list = new ArrayList<>();
User user1 = new User();
user1.setId(1).setAge(15).setName("Axxx").setSex(1);
User user2 = new User();
user2.setId(1).setAge(20).setName("Mxxx").setSex(2);
User user3 = new User();
user3.setId(1).setAge(25).setName("Sxxx").setSex(1);
list.add(user1);
list.add(user2);
list.add(user3);

```

## 流创建

+ 用集合创建

```java
List<String> list = Arrays.asList("a", "b", "c");
// 创建一个顺序流
Stream<String> stream = list.stream();
// 创建一个并行流
Stream<String> parallelStream = list.parallelStream();
```

+ 用数组创建

```java
int[] array={1,3,5,6,8};
IntStream stream = Arrays.stream(array);
```

+ 静态方法创建

```java
Stream<Integer> stream = Stream.of(1, 2, 3, 4, 5, 6);
// 0，3，6，9
Stream<Integer> stream2 = Stream.iterate(0, x -> x + 3).limit(4);
stream2.forEach(System.out::println);
// 三个double值
Stream<Double> stream3 = Stream.generate(Math::random).limit(3);
stream3.forEach(System.out::println);
```

## sorted 排序

+ 自然排序
  + 自然排序

```java
list.stream().sorted();
list.stream().sorted(Comparator.reverseOrder());
```

+ 属性排序
  + Comparater  指定字段排序

```java
// 年龄升序
list.stream().sorted(Comparator.comparing(User::getAge));
// 降序
list.stream().sorted(Comparator.comparing(User::getAge).reversed();
// 混合排序-- 年龄降序，姓名升序
List<User> newList = list
                .stream()
                .sorted(Comparator
                        .comparing(User::getAge, Comparator.reverseOrder())
                        .thenComparing(User::getName))
                .collect(Collectors.toList());
```





## filter 过滤

+ equals

```java
// 排除姓名为 "Mxxx"的用户
List<User> result = list
    .stream()
    .filter(user -> !"Mxxx".equals(user.getName))
    .collect(Collectors.toList());
```

+ list

```java
// 姓名为  "Mxxx"，"Sxxx"
List<String> nameList = new ArrayList<>();
nameList.add("Mxxx");
nameList.add("Sxxx");
List<User> filterResult = list
    .stream()
    .filter(user -> nameList.contains(user.getName()))
    .collect(Collectors.toList());
```

+ findAny+orElse

```java
// 姓名为"Mxxx" 如果有 返回记录，木有则返回null
User result = list
    .stream()
    .filter(user -> "Mxxx".equals(user.getName))
    .findAny()
    .orElse(null);
```

+ 转并行流处理
  + 数据量大时，并行流的处理速度远高于顺序流，但并行流处理数据会打乱顺序

```java
List<User> filterResult = list
    .stream()
    .parallel()
    .filter(user -> user.getAge() > 10)
    .collect(Collectors.toList());
```



## map

+ 元素提取

```java
// 取姓名
List<String> nameList = new ArrayList<>();
nameList = list
    .stream()
    .map(User::getName)
    .collect(Collectors.toList());
```

+ 操作

```java
// 每个元素+3
List<Integer> intList = Arrays.asList(1, 3, 5, 7, 9);
List<Integer> intListNew = intList.stream().map(x -> x + 3).collect(Collectors.toList());
// 转大写（小写toLowerCase） 均可结合去重，排序等
List<String> upperCase = new ArrayList<>();
upperCase = nameList
    .stream()
    .map(String::toUpperCase)
    .collect(Collectors.toList());
```

+ 使用map进行流处理的时候入参是 `Function`  所以是有返回值的；如果不需要返回值，仅需要流操作的时候，可使用peek()

## reduce归约

```java
// sum = 2360
List<Integer> list = Arrays.asList(1, 3, 23, 8, 2311, 14);
// 三种求和方式
Optional<Integer> sum1 = list.stream().reduce((x, y) -> x + y);
Optional<Integer> sum2 = list.stream().reduce(Integer::sum);
Integer sum3 = list.stream().reduce(0, Integer::sum);

// 求乘积 17859408
Optional<Integer> product = list.stream().reduce((x, y) -> x * y);

// 取最大值 2311
Optional<Integer> max1 = list.stream().reduce((x, y) -> x > y ? x : y);
Integer max2 = list.stream().reduce(1, Integer::max);
```



##  distinct

+ 去重

```java
User user4 = new User();
user4.setId(4).setAge(30).setName("Axxx");
list.add(user4);
// 单属性list去重
List<String> distinctList = nameList
    .stream()
    .distinct()
    .collect(Collectors.toList());
```

+ 自定义去重
  + 定义去重处理器，应用断言接口，仅输出布尔类型
  + 自定义属性去重本质上是应用filter实现过滤

```java
public static <T> Predicate<T> distinctByKey(Function<? super T, Object> keyExtractor) {
    Map<Object, Boolean> seen = new ConcurrentHashMap<>();
    return object -> seen.putIfAbsent(keyExtractor.apply(object), Boolean.TRUE) == null;
}
```

```java
List<User> distinctUsers = users
    .stream()
    .filter(DistinctByKey(User::getName))
    .collect(Collectors.toList());
```

## 统计计算

### 计数

```java
Long count = users
    .stream()
    .filter(user -> nameList.contains(user.getName()))
    .count()
```

### 计算

+ BigDecimal求和

```java
BigDecimal sum =list.stream().map(Entity::getValue).reduce(BigDecimal.ZERO,BigDecimal::add);
```

+ int、double、long求和

```java
double sum = list.stream().mapToDouble(Entity::getValue).sum();
```

### 取极值

```java
list.stream().filter(e -> e != null).max(Comparator.naturalOrder()).orElse(null);
list.stream().filter(e -> e != null).min(Comparator.naturalOrder()).orElse(null);
```

### 分组统计

+ 按性别代码分组统计
+ *SummaryStatistics ：摘要统计器，前缀指定类型，示例统计int型，还有long，double类型的统计器
  + 指定的类型是统计类型，即示例中第二个CollCectors的类型 ，统计后返回的count仍为long型
+ 提供了计数，求和，极值等
+ 两个CollCectors：第一个指定的是分组字段，第二个指定的是统计字段
  + count使用分组值统计 （性别分组）
  + Average，Sum，Max，Min按统计值统计（年龄统计）

```java
Map<Integer, IntSummaryStatistics> sexMap = list
	.stream()
	.collect(Collectors.groupingBy(User::getSex, CollCectors.summarizingInt(User::getAge)));
```

## 范围选取

+ skip 跳过元素
+ limit 取元素数
+ 本质上是去除前n个元素和取前n个元素，所以理论上结合使用可以实现分页

```java
List<Integer> list = new ArrayList<>();
      list.add(1);
      list.add(2);
      list.add(3);
      list.add(4);
      list.add(5);
      list.add(6);
// 效果同上
List<Integer> list = Stream.of(1,2,3,4,5,6).collect(Collectors.toList());

List<Integer> result = new ArrayList<>();
// 3,4,5,6
result = list.stream().skip(2).collect(Collectors.toList());
// 1,2
result = list.stream().limit(2).collect(Collectors.toList());
// 3,4
result = list.stream().skip(2).limit(2).collect(Collectors.toList());

```


