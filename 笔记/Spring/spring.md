# @Order

Order注解可以定义Bean在spring aop中的执行优先级, 里面有一个int参数, 值越小, 执行的优先级越高, 默认是最低优先级

# @Aspect

该注解可以将一个bean定义为一个切面

关于aop有三个概念:

1. 切面
   1. 使用@Aspect注解定义
2. 切入点
   1. @PointCut
3. 通知
   1. 前置通知
   2. 后置通知
   3. 返回通知
   4. 异常通知
   5. 环绕通知

# implements InitializingBean, ApplicationContextAware

## ApplicationContextAware

当一个类实现了这个接口之后，这个类就可以方便地获得ApplicationContext对象, 从而获取到ioc容器中所有已经实例化好的bean对象.

Spring发现某个Bean实现了ApplicationContextAware接口，Spring容器会在创建该Bean之后，自动调用该Bean的setApplicationContextAware()方法，调用该方法时，会将容器本身ApplicationContext对象作为参数传给该方法。

使用场景: 我们并不能在所有类中都可以方便的使用ioc注入属性, 例如Utils中要使用dao对象, 这个时候就可以借助ApplicationContextAware接口实现

## InitializingBean

InitializingBean接口为bean提供了初始化方法的方式，它只包括afterPropertiesSet方法，凡是继承该接口的类，在初始化bean的时候会执行该方法。

我们可以通过这个接口实现一些初始化操作, 当容器启动时做一些初始化操作

# @resource

优先使用bean的ID进行匹配, ID匹配不到再用数据类型进行匹配

# @Scope()

spring中的@Controller、@Service、@Dao在容器中默认创建的都是单例的，不是线程安全的，@Scope可以指定bean创建为单例的还是多例的

```java
@Scope(value = "prototype") // 加上@Scope注解，他有2个取值：单例-singleton 多实例-prototype
```

# @ControllerAdvice

定义controller层的切面

## @ExceptionHandler

可以配合@ControllerAdvice定义特定异常的处理方法，在controller层拦截来自service层的异常

```java
@ExceptionHandler(Exception.class)
public ResultVO handleException(Exception e) {
    e.printStackTrace();
    return ResultVO.createFailVO(CodeEnum.EX_UNKONWN.getCode(), e.getMessage());
}
```

## ResponseBodyAdvice接口

实现这个接口，搭配@ControllerAdvice注解，可以自己实现二次封装controller层返回给客户端的json结果

```java
@ControllerAdvice
public class MyResponseBodyAdvice implements ResponseBodyAdvice{
    
    @Override
    // 处理返回数据
    public Object beforeBodyWrite(Object returnValue, MethodParameter methodParameter,
            MediaType mediaType, Class clas, ServerHttpRequest serverHttpRequest,
            ServerHttpResponse serverHttpResponse) {
       Messages<?> msg=(Messages<?>) returnValue;
        //统一修改返回值/响应体
        msg.setXXX("测试修改返回值");
        //返回修改后的值
        return msg;
    }

    @Override
    // 判断controller层的方法是否需要拦截
    public boolean supports(MethodParameter methodParameter, Class clas) {
        //获取当前处理请求的controller的方法
        String methodName=methodParameter.getMethod().getName(); 
        // 不拦截/不需要处理返回值 的方法
        String method= "loginCheck"; //如登录
        //不拦截
        return !method.equals(methodName);
    }
}
```

# Spring定时任务的几种实现

[Spring定时任务的几种实现 - 简书 (jianshu.com)](https://www.jianshu.com/p/fb3c768c2256)

# 利用@ControllerAdvice和ResponseBodyAdvice接口统一处理返回值

https://my.oschina.net/diamondfsd/blog/3069546

**作用**：可以对controller层做一些通用处理

主要依赖两个方法：supports和beforeBodyWrite

重写supports方法：可以自定义那些返回值类型需要做返回值的统一处理

重写beforeBodyWrite方法：统一处理返回值





