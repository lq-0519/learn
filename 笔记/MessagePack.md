# MessagePack简介

[MessagePack简介及使用 - 简书 (jianshu.com)](https://www.jianshu.com/p/8c24bef40e2f)

It’s like JSON, but fast and small.

MessagePack是一种标准, 并不是一种软件

MessagePack在速度和体积上都有很大的优势

## 原理

![img](https://upload-images.jianshu.io/upload_images/5142564-d21f57d223d1dc92.png?imageMogr2/auto-orient/strip|imageView2/2/format/webp)

图上这个json长度为27字节，但是为了表示这个数据结构，它用了9个字节（就是那些大括号、引号、冒号之类的，他们是白白多出来的）来表示那些额外添加的无意义数据。msgpack的优化在图上展示的也比较清楚了，省去了特殊符号，用特定编码对各种类型进行定义

**此外, 对于过长的String和数组, MessagePack有自己的压缩方式**

