# Fastjson

## Fastjson 枚举处理

~~~ java
// message 是一个带有 SeriesButton 枚举属性的实例
SerializeConfig config = new SerializeConfig();
config.configEnumAsJavaBean(SeriesButton.class);
String message = JSON.toJSONString(message, config);
~~~

## references

[Java 枚举 - fastjson 对 enum 的处理](https://www.cnblogs.com/VergiLyn/p/6753706.html)
