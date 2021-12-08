## 常用的几个注解

@Data ： 注在类上，提供类的get、set、equals、hashCode、canEqual、toString方法
 @AllArgsConstructor ： 注在类上，提供类的全参构造
 @NoArgsConstructor ： 注在类上，提供类的无参构造
 @Setter ： 注在属性上，提供 set 方法
 @Getter ： 注在属性上，提供 get 方法
 @EqualsAndHashCode ：    注在类上，提供对应的 equals 和 hashCode 方法
 @Log4j/@Slf4j   ： 注在类上，提供对应的 Logger 对象，变量名为 log

# @Data

这个注解已经包含了@EqualsAndHashCode

# @EqualsAndHashCode 问题

有了@EqualsAndHashCode注解，那么就会在此类中存在equals(Object other) 和 hashCode()方法，且不会使用父类的属性，这就导致了可能的问题。
比如，有多个类有相同的部分属性，把它们定义到父类中，恰好id（数据库主键）也在父类中，那么就会存在部分对象在比较时，它们并不相等，却因为lombok自动生成的equals(Object other) 和 hashCode()方法判定为相等，从而导致出错。

修复此问题的方法很简单：
1. 使用@Getter @Setter @ToString代替@Data并且自定义equals(Object other) 和 hashCode()方法，比如有些类只需要判断主键id是否相等即足矣。
2. 或者使用在使用@Data时同时加上@EqualsAndHashCode(callSuper=true)注解。



















