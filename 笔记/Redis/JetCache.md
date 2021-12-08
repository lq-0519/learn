# @CacheUpdate

用于更新缓存

更新缓存时, 需要自己指定要更新缓存的key和value

代码示例:

```java
@CacheUpdate(name = "JsfActivityPlanRpc.activityPlanHelpList", key = "#activityPlanHelpRpcQuery.id + '_' + #page + '_' + #pageSize", value = "#activityPlanHelpVOPageVO")
void updateActivityPlanHelpList(ActivityPlanHelpQuery activityPlanHelpRpcQuery, int page, int pageSize, String activityPlanHelpVOPageVO);
```

使用时, 我们需要手动调用updateActivityPlanHelpList方法, 并将缓存的key和value作为入参传到方法里去, activityPlanHelpVOPageVO参数就是缓存的值

# 私有方法

不可以给私有方法添加注解使用缓存, 私有方法切不到, 加了也无效

不仅可以给接口方法加注解, 也快给实现类的方法加注解

