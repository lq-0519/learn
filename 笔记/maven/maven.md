# 为什么要先clean一下

理论上不使用mvn clean命令打出来的包也是最新的, 但也有例外, 除非你使用了其他的方式修改了jar包中的内容而没有修改源代码.

使用maven打包时, maven会先判断引用的外部jar包是不是最新的, 如果判断是最新的, 就不会重新打jar包, 而此时如果你修改了jar包的内容, 那么新修改的内容就不会更新到最新的包中

最保险的办法就是在打包之前执行以下mvn clean命令

# jar包冲突

http://shendeng.jd.com/article/detail/1121

## 产生冲突的原因

maven引入第三方jar包的时候可能会引入一些其他依赖的jar包，所以可能会出现同一个jar包存在两个不同的版本，而maven在寻找jar包的时候遵循**最短路径原则**，但找到的可能不是我们需要的jar包，就出现了jar包冲突，会报出ClassNotFoundException、NoSuchMethodError异常

还有一种原因是，类的全限定类名是一样的，但是出现在不同的jar包中，在没有自定义类加载器的情况下就可能加载错误的类。这种冲突的情况在实际的开发中很少遇到

## 最短路径原则

maven在选中jar包时会选择依赖路径最短的jar包。例如：A依赖B、B依赖C、C依赖D，这样就形成了一个A->B->C->D的一个依赖路径，假设还有一个依赖路径是E->F->D，那当我们同时导入jar包A和jar包E的时候，jar包D就会选择E中的那个版本，因为E的依赖路径更短一些

## 优先声明原则

当两个依赖路径长度相同时，最短路径原则就不在适用了。如果存在A->B->C、E->F->C两个依赖路径，当同时导入A和E时，maven就会按照A和E在pom中声明的位置从上到下进行jar包选中

## 依赖管理

当使用dependencyManagement标签进行jar包管理时，最短路径原则和优先声明原则将不再生效，jar包版本有标签进行决定

## 冲突解决办法

### exclusions

使用maven提供的包排除标签排除我们不需要的jar包，在实际开发中我们可以使用idea的插件maven helper来轻松处理。

但是每次出现有传递性的jar包被引入的时候，且出现jar包冲突就需要使用exclusions进行排包处理，比较麻烦，更推荐使用标签dependencyManagement来统一管理jar包

### dependencyManagement

使用dependencyManagement统一管理即可