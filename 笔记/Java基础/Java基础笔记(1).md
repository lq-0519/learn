# 枚举

1. 枚举里面都是常量，都是被public final static修饰的
2. 枚举是一个特殊的类，枚举类
3. 枚举类里面定义的第一行变量都是当前对象的实例对象
4. 枚举值使用方法
   1. 枚举类的名字.枚举类的实例名

# 对象拷贝

## 使用json方式

1. JSON.parseObject(JSON.toJSONString(bdsSymbolResult), DifferenceGoodsVO.class)
   1. 现使用toJSONString将bdsSymbolResult转化为json字符串, 在使用parseObject将cson字符串转换为DifferenceGoodsVO对象
2. 返回值是一个DifferenceGoodsVO类型
3. 依靠属性名来拷贝, 仅拷贝属性名字相同的属性
4. 性能差

## PropertyUtils和BeanUtils

### org.apache.commons.beanutils.PropertyUtils#copyProperties

1. 拷贝属性名和属性类型相同的
2. 是严格的类型转换
3. copyProperties(A,B); 是B中的值付给A

### org.springframework.beans.BeanUtils#copyProperties(java.lang.Object, java.lang.Object)

1. 拷贝属性名相同的
2. copyProperties(A,B);  是A中的值付给B
3. 拷贝时是一种覆盖操作

### 二者的区别

1. PropertyUtils支持为null的场景, BeanUtils部分为null的场景不支持

## 深拷贝和浅拷贝

首先明白一个知识点，在Java中，对于基本数据类型和引用数据类型在进行赋值时有值传递和引用传递之分。

### 浅拷贝

进行的是引用传递，这样两个对象指向同一片内存，操作其中一个对象时也会影响另一个

### 深拷贝

对象图：无论多么复杂的对象都是由基本数据类型组成的，一个对象的成员变量属性可以是一个对象，这个属性的属性也可以是一个对象，最终一定会有一个属性是有基本数据类型构成，这样的一个链式关系就是对象图。

深拷贝不仅给基本数据类型的成员变量进行了赋值，还给所有引用数据类型的成员变量开启新的存储空间，并复制每个引用数据类型成员变量引用的对象，直到对象所有可达的对象。也就是说，深拷贝是对整个对象图进行了拷贝。

#### 深拷贝的实现

通过实现Cloneable接口并重写clone方法实现深拷贝，每一层对象都需要执行这个操作才行。

# 日志级别

1. log4j定义了8个级别的log（除去OFF和ALL，可以说分为6个级别），优先级从高到低依次为：OFF、FATAL、ERROR、WARN、INFO、DEBUG、TRACE、 ALL
2. 日志级别越低, 可以理解为越容易打印出来
3. 线上的日志级别是WARN
4. 测试环境的日志级别是DEBUG

# Objects::isNull

1. 仅仅自处理流时使用这个方法即可

# Java8 Stream流

1. https://juejin.cn/post/6844903830254010381

2. https://www.exception.site/java8/java8-new-features

3. 只能对实现了 java.util.Collection 接口的类做流的操作

   1. Map 不支持 Stream 流。


## 中间操作

1. 方法的返回值是Stream, 可以继续向下拼接Stream流

### Map转换

1. 可以往里面传递一个方法, 对list集合中的每一个元素进行相关操作
2. eg: .map(String::toUpCase)
   1. 转换为大写

### Filter 过滤

### Sorted 排序

1. `Sorted` 同样是一个中间操作，它的返参是一个 `Stream` 流
2. 我们可以传入一个 `Comparator` 用来自定义排序，如果不传，则使用默认的排序规则。
   1. 需要注意，`sorted` 不会对 `stringCollection` 做出任何改变，`stringCollection` 还是原有的那些个元素，且顺序不变

## 终端操作

1. 返回值不是Stream, 不可以继续向下拼接了

### Match匹配

1. 是一个终端操作
2. .anyMatch()
   1. 有一个匹配就返回true
3. .allMatch()
   1. 全部匹配返回true
4. .noneMatch()
   1. 都不匹配返回true
5. 都是传入lamda表达式

### count操作

1. 终端操作
2. 返回流中剩余元素的个数, Long类型的返回值

### collect

是一个终端操作, 将流中的内容转换为一个map或集合

#### Collectors.groupingBy

分组

##### 代码示例

分组计数

```java
List<String> items = Arrays.asList("apple", "apple", "banana", "apple", "orange", "banana", "papaya");
Map<String, Long> result = items.stream()
    .collect(Collectors.groupingBy(Function.identity(), Collectors.counting()));
// papaya=1, orange=1, banana=2, apple=3
System.out.println(result);
```

分组

```java
List<String> items = Arrays.asList("apple", "apple", "banana", "apple", "orange", "banana", "papaya");
Map<String, List<String>> result = items.stream()
        .collect(Collectors.groupingBy(Function.identity()));
// {papaya=[papaya], orange=[orange], banana=[banana, banana], apple=[apple, apple, apple]}
System.out.println(result);

ArrayList<Man> list = Lists.newArrayList(
        new Man(22, "11"),
        new Man(23, "12"),
        new Man(24, "13"),
        new Man(21, "14"),
        new Man(22, "15"),
        new Man(21, "16"),
        new Man(25, "17"),
        new Man(26, "18"),
        new Man(24, "19"),
        new Man(23, "10"),
        new Man(23, "21"),
        new Man(29, "22")
);
Map<Integer, List<Man>> collect = list.stream()
        .collect(Collectors.groupingBy(Man::getAge));
System.out.println(JSON.toJSONString(collect));
```

**Collectors.mapping**

```java
public static void main(String[] args) {
    //3 apple, 2 banana, others 1
    List<Item> items = Arrays.asList(
        new Item("apple", 10, new BigDecimal("9.99")),
        new Item("banana", 20, new BigDecimal("19.99")),
        new Item("orang", 10, new BigDecimal("29.99")),
        new Item("watermelon", 10, new BigDecimal("29.99")),
        new Item("papaya", 20, new BigDecimal("9.99"))
    );
    // group by price, uses 'mapping' to convert List<Item> to Set<String>
    Map<BigDecimal, Set<String>> result =
        items.stream()
        .collect(Collectors.groupingBy(Item::getPrice,
                                       Collectors.mapping(Item::getName, Collectors.toSet())));
    System.out.println(result);
}
```



## Arrays.asList

可以将一个数组直接转换为一个stream流

## Function.identity()

Function.identity()返回一个输出跟输入一样的Lambda表达式对象，等价于形如`t -> t`形式的Lambda表达式

使用场景

```java
// 将Stream转换成容器或Map
Stream<String> stream = Stream.of("I", "love", "you", "too");
// 流中的元素原封不动作为生成的map的key
Map<String, Integer> map = stream.collect(Collectors.toMap(Function.identity(), String::length));
```

# 集合

初始化集合时尽量设置好集合的大小，避免频繁扩容带来的额外开销。

## List

list集合可以往里面放null值，而且可以放好多个。

# 其他

try中的return语句执行后程序不会立即结束，还会执行finally代码块中的代码，如果finally中有return语句，程序就会结束，抛弃try中的return语句

```java
testReturn(){
  int x=0;
  try{
    // x=1, 但方法不会立即返回
    return ++x;
  }finally{
    // x=2，方法返回2
    return ++x;
  }
}
```

# 获取当前时间的三种方式

| 方式                                     | 耗时(ns) |
| :--------------------------------------- | -------- |
| Calendar.getInstance().getTimeInMillis() | 26785903 |
| System.currentTimeMillis()               | 9439     |
| new Date().getTime()                     | 255603   |

结果的差异应是创建的耗时原因，api的对象创建耗时时间如上所示。从以上数据可以看出System.currentTimeMillis() 耗时很少，几乎不用创建对象，所以比较快

# cronExpression

cronExpression主要的格式：{秒数} {分钟} {小时} {日期} {月份} {星期} {年份(可为空)}

## ”?”

”?” 来实现互斥

比如：3 3 3 ？* 1（每周1，3点3分3秒执行一次） 3 3 3 * * ？（每天的3点3分3秒执行一次）

## 经典案例

```
"30 * * * * ?" 每半分钟触发任务

"30 10 * * * ?" 每小时的10分30秒触发任务

"30 10 1 * * ?" 每天1点10分30秒触发任务

"30 10 1 20 * ?" 每月20号1点10分30秒触发任务

"30 10 1 20 10 ? *" 每年10月20号1点10分30秒触发任务

"30 10 1 20 10 ? 2011" 2011年10月20号1点10分30秒触发任务

"30 10 1 ? 10 * 2011" 2011年10月每天1点10分30秒触发任务

"30 10 1 ? 10 SUN 2011" 2011年10月每周日1点10分30秒触发任务

"15,30,45 * * * * ?" 每15秒，30秒，45秒时触发任务

"15-45 * * * * ?" 15到45秒内，每秒都触发任务

"15/5 * * * * ?" 每分钟的每15秒开始触发，每隔5秒触发一次

"15-30/5 * * * * ?" 每分钟的15秒到30秒之间开始触发，每隔5秒触发一次

"0 0/3 * * * ?" 每小时的第0分0秒开始，每三分钟触发一次

"0 15 10 ? * MON-FRI" 星期一到星期五的10点15分0秒触发任务

"0 15 10 L * ?" 每个月最后一天的10点15分0秒触发任务

"0 15 10 LW * ?" 每个月最后一个工作日的10点15分0秒触发任务

"0 15 10 ? * 5L" 每个月最后一个星期四的10点15分0秒触发任务

"0 15 10 ? * 5#3" 每个月第三周的星期四的10点15分0秒触发任务
```

## “/” 代表触发步进(step)

比如”0/20”或者”*/20”代表从0秒钟开始，每隔20秒钟触发1次，即0秒触发1次，20秒触发1次，40秒触发1次；

## 0/10和*/10的区别

起始的时间不一样。例如：从`5:07`分钟的时候执行该任务第一种写法会在`5:10`的时候进行执行，写法二会在`5:17`进行执行。这就是两者的差别。
当然`0 0/1 * * *` 与`0 */1 * * *`有时会被认为是同一种写法。

# Optional

jdk8新增, 可以优雅的处理null

在代码结构上和**java流**非常类似

## Optional.ofNullable(T value)和Optional.of(T value)

使用上面两个方法可以创建一个optional流

代码示例

```java
Optional<User> user = Optional.ofNullable(getUserById(id));
user.ifPresent(u -> System.out.println("Username is: " + u.getUsername()));
```

## 中间操作

方法的返回值是一个Optional, 可以继续往下操作

### map

类似于java流中的操作, 可以转换Optional中的数据类型

### filter

过滤操作

## 终端操作

返回值是void或Optional中的value, 不可以继续在往下操作了

### ifPresent

如果value的值不为null, 可以执行一些指定的操作

### orElseGet

如果value为null, 可以执行orElseGet方法中指定的方法, 并将方法的返回值作为1流的结果

# serialVersionUID

[Java serialVersionUID 有什么作用？ - 简书 (jianshu.com)](https://www.jianshu.com/p/91fa3d2ac892)

**作用: **保证在进行反序列化时, 发送方和接收方得到的都是可兼容的对象

**显示定义序列化ID:** java可以根据类的多个方面生成一个默认的序列化ID, 但是, 还是推荐自己再显示的定义一个序列化ID, 因为不同的jdk版本可能产生不同的序列化ID

**可能产生的问题:**当类新增字段时, java自动生成的序列化ID就可能发生变化









