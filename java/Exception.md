# Exception

## java.lang.IllegalArgumentException

配置异步线程池

[reference](https://www.cnblogs.com/zcz527/p/9483055.html)

`@EnableAsync`
`@EnableAspectJAutoProxy(exposeProxy = true)`

```java
@Configuration
public class AsyncConfiguration implements AsyncConfigurer{}
```

```java
Caused by: org.springframework.beans.BeanInstantiationException: Failed to instantiate [org.springframework.scheduling.concurrent.ThreadPoolTaskExecutor]: Illegal arguments to factory method 'executor'; args: ; nested exception is java.lang.IllegalArgumentException: object is not an instance of declaring class
 at org.springframework.beans.factory.support.SimpleInstantiationStrategy.instantiate(SimpleInstantiationStrategy.java:172)
 at org.springframework.beans.factory.support.ConstructorResolver.instantiate(ConstructorResolver.java:650)
 ... 20 common frames omitted
Caused by: java.lang.IllegalArgumentException: object is not an instance of declaring class
 at sun.reflect.NativeMethodAccessorImpl.invoke0(Native Method)
 at sun.reflect.NativeMethodAccessorImpl.invoke(NativeMethodAccessorImpl.java:62)
 at sun.reflect.DelegatingMethodAccessorImpl.invoke(DelegatingMethodAccessorImpl.java:43)
 at java.lang.reflect.Method.invoke(Method.java:498)
 at org.springframework.beans.factory.support.SimpleInstantiationStrategy.instantiate(SimpleInstantiationStrategy.java:154)
 ... 21 common frames omitted
```
