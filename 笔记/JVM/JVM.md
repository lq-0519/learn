# -Xss

指定线程空间大小

## 线程数量

（机器本身可用内存-JVM分配的堆内存）/Xss的值，比如我们的容器本身大小是8G,堆大小是4096M,走-Xss默认值，可以得出 最大线程数量：4096个

jvm内存不能配置的过大, 太大物理机就没有创建线程的内存了

# java应用容器化后jvm配置细节

[JVM 对 docker 容器 CPU 限制的兼容](https://www.jianshu.com/p/040a1315bce5)

[Java服务器可以跑多少个线程以及Docker下GC线程数的问题](https://hogwartsrico.github.io/2019/10/16/JavaThread/index.html)

修改JVM启动参数:, 4C8G容器：

```
-XX:ParallelGCThreads=4 -XX:ConcGCThreads=2
```

ParallelGCThreads值建议等于CPU核数

ConcGCThreads值建议等于(ParallelGCThreads+3)/4

`-XX:ParallelGCThreads=n` 设置垃圾收集器在**并行**阶段使用的线程数
`-XX:ConcGCThreads=n` **并发**垃圾收集器使用的线程数量

这种临时方案也有缺点，因为一些第三方框架也会根据获取到的CPU数量调整框架的线程数量，比如并行流parallelStream会获取实际物理机的核心数, 可能会开启超多线程, 使容器出现故障, 为了根本上解决此问题，需要JVM可以正确的获取到容器限制的CPU数量、内存大小等。







