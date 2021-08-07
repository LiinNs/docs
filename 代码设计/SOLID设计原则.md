# SOLID principle 代码设计原则

## Single 单一职责

筷子用来吃饭，不用来击打架子鼓

## OpenClose 开闭原则 对拓展开放、对修改闭合

支付 <- 阿里支付
支付 <- 微信支付
支付 <- 银联支付
...PayPal
搅拌器、水牙线、电动牙刷

## Liskov Substitution 里氏替换原则

长方形 <- 正方形 ×
运动员 <- 举重运动员 √

## Interface Segregation 接口隔离原则

聚合支付、独立支付

## Dependency Inversion 依赖反转

依赖接口编程，不依赖具体实现。
妈妈读书
妈妈、爸爸、爷爷、奶奶... Teller接口 tellStory
读书、读报纸、看网页、杂志 IReader接口 getContent
Driver
Car

## references

[1](https://www.cnblogs.com/wuyuegb2312/p/7011708.html)
[2](https://insights.thoughtworks.cn/understand-solid-principles/)
[3](https://insights.thoughtworks.cn/what-is-solid-principle/)
[4](https://segmentfault.com/a/1190000022384751)
[5](https://xie.infoq.cn/article/53c6f7a700d94558db090efbf)
