---
title: RabbitMQ简单使用教程
category:
  - RabbitMQ
tags:
  - RabbitMQ
abbrlink: e62ed69c
date: 2018-06-28 08:20:17
---

#### 最简单的使用

##### 1、 引用 rabbitMQ.Client
```
Install-Package RabbitMQ.Client
```


##### 2、 创建生产者

```
//创建连接工厂
ConnectionFactory factory = new ConnectionFactory
{
    UserName = "mqAdmin",//用户名
    Password = "mqAdmin",//密码
    HostName = "192.168.1.103"//rabbitmq ip
};

//创建连接
var connection = factory.CreateConnection();
//创建通道
var channel = connection.CreateModel();
//声明一个队列
channel.QueueDeclare("hello", false, false, false, null);

Console.WriteLine("\nRabbitMQ连接成功，请输入消息，输入exit退出！");

string input;
do
{
    input = Console.ReadLine();

    var sendBytes = Encoding.UTF8.GetBytes(input);
    //发布消息
    channel.BasicPublish("", "hello", null, sendBytes);

} while (input.Trim().ToLower()!="exit");
channel.Close();
connection.Close();
```

##### 3、 创建生产者



```
//创建连接工厂
ConnectionFactory factory = new ConnectionFactory
{
    UserName = "admin",//用户名
    Password = "admin",//密码
    HostName = "192.168.157.130"//rabbitmq ip
};

//创建连接
var connection = factory.CreateConnection();
//创建通道
var channel = connection.CreateModel();

//事件基本消费者
EventingBasicConsumer consumer = new EventingBasicConsumer(channel);

//接收到消息事件
consumer.Received += (ch, ea) =>
{
    var message = Encoding.UTF8.GetString(ea.Body);
    Console.WriteLine($"收到消息： {message}");
    //确认该消息已被消费
    channel.BasicAck(ea.DeliveryTag, false);
};
//启动消费者 设置为手动应答消息
channel.BasicConsume("hello", false, consumer);
Console.WriteLine("消费者已启动");
Console.ReadKey();
channel.Dispose();
connection.Close();
```
 <!-- more -->

#### RabbitMQ消费失败的处理

RabbitMQ采用消息应答机制，即消费者收到一个消息之后，需要发送一个应答，然后RabbitMQ才会将这个消息从队列中删除，如果消费者在消费过程中出现异常，断开连接切没有发送应答，那么RabbitMQ会将这个消息重新投递。


```
//接收到消息事件
consumer.Received += (ch, ea) =>
{
    var message = Encoding.UTF8.GetString(ea.Body);

    Console.WriteLine($"收到消息： {message}");

    Console.WriteLine($"收到该消息[{ea.DeliveryTag}] 延迟10s发送回执");
    Thread.Sleep(10000);
    //确认该消息已被消费
    channel.BasicAck(ea.DeliveryTag, false);
    Console.WriteLine($"已发送回执[{ea.DeliveryTag}]");
};
```

#### 使用RabbitMQ的Exchange

前面我们可以看到生产者将消息投递到Queue中，实际上这在RabbitMQ中这种事情永远都不会发生。实际的情况是，生产者将消息发送到Exchange（交换器），由Exchange将消息路由到一个或多个Queue中（或者丢弃）

AMQP协议中的核心思想就是生产者和消费者隔离，生产者从不直接将消息发送给队列。生产者通常不知道是否一个消息会被发送到队列中，只是将消息发送到一个交换机。先由Exchange来接收，然后Exchange按照特定的策略转发到Queue进行存储。同理，消费者也是如此。Exchange 就类似于一个交换机，转发各个消息分发到相应的队列中。

RabbitMQ提供了四种Exchange模式：direct,fanout,topic,header 。但是 header模式在实际使用中较少，所以这里只介绍前三种模式。

> Exchange不是消费者关心的，所以消费者的代码完全不用变，用上面的消费者就行了。

##### Direct Exchange

所有发送到Direct Exchange的消息被转发到具有指定RouteKey的Queue。

Direct模式,可以使用rabbitMQ自带的Exchange：default Exchange 。所以不需要将Exchange进行任何绑定(binding)操作 。消息传递时，RouteKey必须完全匹配，才会被队列接收，否则该消息会被抛弃。


```
//创建连接
var connection = factory.CreateConnection();
//创建通道
var channel = connection.CreateModel();

//定义一个Direct类型交换机
channel.ExchangeDeclare(exchangeName, ExchangeType.Direct, false, false, null);

//定义一个队列
channel.QueueDeclare(queueName, false, false, false, null);

//将队列绑定到交换机
channel.QueueBind(queueName, exchangeName, routeKey, null);
```


##### Fanout Exchange

所有发送到Fanout Exchange的消息都会被转发到与该Exchange 绑定(Binding)的所有Queue上。

Fanout Exchange 不需要处理RouteKey 。只需要简单的将队列绑定到exchange 上。这样发送到exchange的消息都会被转发到与该交换机绑定的所有队列上。类似子网广播，每台子网内的主机都获得了一份复制的消息。

所以，Fanout Exchange 转发消息是最快的。

为了演示效果，定义了两个队列，分别为hello1，hello2，每个队列都拥有一个消费者。


```
static void Main(string[] args)
{
    string exchangeName = "TestFanoutChange";
    string queueName1 = "hello1";
    string queueName2 = "hello2";
    string routeKey = "";

    //创建连接工厂
    ConnectionFactory factory = new ConnectionFactory
    {
        UserName = "admin",//用户名
        Password = "admin",//密码
        HostName = "192.168.157.130"//rabbitmq ip
    };

    //创建连接
    var connection = factory.CreateConnection();
    //创建通道
    var channel = connection.CreateModel();

    //定义一个Direct类型交换机
    channel.ExchangeDeclare(exchangeName, ExchangeType.Fanout, false, false, null);

    //定义队列1
    channel.QueueDeclare(queueName1, false, false, false, null);
    //定义队列2
    channel.QueueDeclare(queueName2, false, false, false, null);

    //将队列绑定到交换机
    channel.QueueBind(queueName1, exchangeName, routeKey, null);
    channel.QueueBind(queueName2, exchangeName, routeKey, null);

    //生成两个队列的消费者
    ConsumerGenerator(queueName1);
    ConsumerGenerator(queueName2);


    Console.WriteLine($"\nRabbitMQ连接成功，\n\n请输入消息，输入exit退出！");

    string input;
    do
    {
        input = Console.ReadLine();

        var sendBytes = Encoding.UTF8.GetBytes(input);
        //发布消息
        channel.BasicPublish(exchangeName, routeKey, null, sendBytes);

    } while (input.Trim().ToLower() != "exit");
    channel.Close();
    connection.Close();
}

/// <summary>
/// 根据队列名称生成消费者
/// </summary>
/// <param name="queueName"></param>
static void ConsumerGenerator(string queueName)
{
    //创建连接工厂
    ConnectionFactory factory = new ConnectionFactory
    {
        UserName = "admin",//用户名
        Password = "admin",//密码
        HostName = "192.168.157.130"//rabbitmq ip
    };

    //创建连接
    var connection = factory.CreateConnection();
    //创建通道
    var channel = connection.CreateModel();

    //事件基本消费者
    EventingBasicConsumer consumer = new EventingBasicConsumer(channel);

    //接收到消息事件
    consumer.Received += (ch, ea) =>
    {
        var message = Encoding.UTF8.GetString(ea.Body);

        Console.WriteLine($"Queue:{queueName}收到消息： {message}");
        //确认该消息已被消费
        channel.BasicAck(ea.DeliveryTag, false);
    };
    //启动消费者 设置为手动应答消息
    channel.BasicConsume(queueName, false, consumer);
    Console.WriteLine($"Queue:{queueName}，消费者已启动");
}
```
##### Topic Exchange


```
所有发送到Topic Exchange的消息被转发到能和Topic匹配的Queue上，

Exchange 将路由进行模糊匹配。可以使用通配符进行模糊匹配，符号“#”匹配一个或多个词，符号“”匹配不多不少一个词。因此“XiaoChen.#”能够匹配到“XiaoChen.pets.cat”，但是“XiaoChen.” 只会匹配到“XiaoChen.money”。

所以，Topic Exchange 使用非常灵活。

string exchangeName = "TestTopicChange";
string queueName = "hello";
string routeKey = "TestRouteKey.*";

//创建连接工厂
ConnectionFactory factory = new ConnectionFactory
{
    UserName = "admin",//用户名
    Password = "admin",//密码
    HostName = "192.168.157.130"//rabbitmq ip
};

//创建连接
var connection = factory.CreateConnection();
//创建通道
var channel = connection.CreateModel();

//定义一个Direct类型交换机
channel.ExchangeDeclare(exchangeName, ExchangeType.Topic, false, false, null);

//定义队列1
channel.QueueDeclare(queueName, false, false, false, null);

//将队列绑定到交换机
channel.QueueBind(queueName, exchangeName, routeKey, null);



Console.WriteLine($"\nRabbitMQ连接成功，\n\n请输入消息，输入exit退出！");

string input;
do
{
    input = Console.ReadLine();

    var sendBytes = Encoding.UTF8.GetBytes(input);
    //发布消息
    channel.BasicPublish(exchangeName, "TestRouteKey.one", null, sendBytes);

} while (input.Trim().ToLower() != "exit");
channel.Close();
connection.Close();
```


---

> 本文转自：http://www.cnblogs.com/stulzq/p/7551819.html
> 本人进行了部分精简。若有侵权，请告知！