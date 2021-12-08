# 监控点(key)

key, 是一个非常重要的概念, 每个监控点都对应一个key, key是全平台唯一的, key命名方法: 应用名.简单类名.方法名

# TP指标

TP99: 方法的每一次调用都有一个消耗时间, 将消耗时间从小到大排序, 能满足前99%的请求的时间就是TP99的值

# 方法心跳

如果一个方法一段代码一定时间内一定会被执行, 我们可以设置方法心跳, 如果方法超时未执行就会报警

# 接入监控侵入式(java)

```java
CallerInfo info = null;
try {
    info = Profiler.registerInfo("you key", false, true); // 监控点开始
    //业务逻辑代码
} catch (Exception e) {
    Profiler.functionError(info); // 此处可以监控方法的可用率
    //判断出错后的异常处理
} finally {
    Profiler.registerInfoEnd(info); // 此处是监控方法的性能
}
```

# 自定义监控

```java
 public static void businessAlarm(String key, long time, String detail) 
// key：在网站上注册的方法监控点的key
// time：本次业务情况发生的时间
// detail:本次业务记录的描述信息
```

