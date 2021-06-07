# Redis Messaging (Pub/Sub)

* Publication or production of messages
* Subscription or consumption of messages

## Publishing (Sending Message)

To publish a message, you can use, as with the other operations, either the low-level `RedisConnection` or the high-level `RedisTemplate`.

```java
ApplicationContext ctx = SpringApplication.run(MessagingRedisApplication.class, args);
StringRedisTemplate template = ctx.getBean(StringRedisTemplate.class);
Receiver receiver = ctx.getBean(Receiver.class);

while (receiver.getCount() == 0) {
  LOGGER.info("Sending message...");
  template.convertAndSend("chat", "Hello from Redis!");
  Thread.sleep(500L);
}

System.exit(0);
```

## Subscribing (Receiving Message)

On the receiving side, one can subscribe to one or multiple channels either by naming them directly or by using pattern matching. 

### Message Listener Container
`RedisMessageListenerContainer` acts as a message listener container. It is used to receive messages from a Redis channel and drive the `MessageListener` instances that are injected into it.

```java
@Bean
RedisMessageListenerContainer container(RedisConnectionFactory connectionFactory,
  MessageListenerAdapter listenerAdapter) {

  RedisMessageListenerContainer container = new RedisMessageListenerContainer();
  container.setConnectionFactory(connectionFactory);
  container.addMessageListener(listenerAdapter, new PatternTopic("chat"));

  return container;
}
```

### MessageListenerAdapter
The `MessageListenerAdapter` class is the final component in Spring’s asynchronous messaging support. 
```java
@Bean
MessageListenerAdapter listenerAdapter(Receiver receiver) {
  return new MessageListenerAdapter(receiver, "receiveMessage");
}
```
[https://docs.spring.io/spring-data/data-redis/docs/current/reference/html/#pubsub](https://docs.spring.io/spring-data/data-redis/docs/current/reference/html/#pubsub)
