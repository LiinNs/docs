# socket

## 协议

[协议](https://www.cnblogs.com/chyingp/p/websocket-deep-in.html)
[协议说明2](https://blog.csdn.net/u010983881/article/details/83513327)

## 消息示例

wss://api.otcbro.com/swap/swap-ws/351/4t45epde/websocket
["CONNECT\naccept-version:1.1,1.0\nheart-beat:10000,10000\n\n\u0000"]
["SUBSCRIBE\nid:sub-0\ndestination:/topic/swap/thumb\n\n\u0000"]
["SUBSCRIBE\nid:sub-1\ndestination:/topic/swap/trade/BTC/USDT\n\n\u0000"]
["SUBSCRIBE\nid:sub-2\ndestination:/topic/swap/trade-plate/BTC/USDT\n\n\u0000"]
["SUBSCRIBE\nid:sub-3\ndestination:/topic/swap/trade/BTC/USDT\n\n\u0000"]
["SUBSCRIBE\nid:sub-4\ndestination:/topic/swap/thumb\n\n\u0000"]
["SUBSCRIBE\nid:sub-5\ndestination:/topic/swap/kline/BTC/USDT\n\n\u0000"]
