# RocketMq消息实践

### 批量消息

同主题多条消息打包发送至消息服务端，减少网络调用次数，提高网络传输效率，payload list -> message list

~~~Java
List<Message>messageList = new ArrayList<>();
producer.send(messageList)
~~~

### 消息发送队列自选择

消息发送默认根据主题的路由信息（主题消息队列）进行负载均衡，负债均衡策略为轮询策略。

例如现在有这样一个场景，订单的状态变更消息发送到特定主题，为了避免消息消费者同时消费同一订单的不同状态的变更消息，我们应该使用顺序消息，为了提高消息消费的并发度，如果我们能根据某种负载算法，相同订单的不同消息能统一到同一个消息消费队列上，则可以避免引人分布式锁， RocketMQ 在消息发送时提供了消息队列选择器 `MessageQueueSelector`

~~~Java
producer.send(msg, new MessageQueueSelector() {
    public MessageQueue select(List<MessageQueue> mqs, Message msg, Object arg) {
        Integer id = (Integer) arg;
        int index = id % mqs.size();
        return mqs.get(index);
    }
}, orderId);
~~~

### 消息过滤

#### TAG

~~~Java
orderconsumer.subscribe("TopicFilter7 ", "TOPICA_TAG_ALL | TOPICA_TAG_ORD");
kuCunConsumer.subscribe("TopicFilter7 "，"TOP CA_TAG_ALL | TOPICA_TAG_CAPCITY");
~~~

#### SQL92

~~~Java
consumer.subscribe("TopicTest", MessageSelector.bySql("(orderStatus is not null and orderStatus > 0)")); 
~~~

#### 类模式

~~~Java
public class MessageFiltermpl implements MessageFilter { 

    @Override
    public boolean match( MessageExt msg, Filtercontext context) { 
        String property = msg.getProperty("SequenceId"); 
        if (property != null) { 
            int id = Integer.parseInt(property); 
            if ( ((id % 10) == 0) && (id > 100)) { 
                return true; 
            }
        }
        return false;
    }
}
~~~

#### 事务消息