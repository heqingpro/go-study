### kafka

#### 一、原理

- **topic**
  
  - 生产者push 到topic，消费者从topic 拉数据

- **Partition** （分区）
  
  - 一个topic 会对应多个分区，消费者从分区取数据
  - partition分布于不同的broker 中
  - 分区在各个broker中做备份
  - 数据写到磁盘来持久化，通过 顺序访问IO和缓存(等到一定的量或时间)才真正把数据写到磁盘上，来提高速度

- **broker**
  
  - 一台kafka 就是一个broker
  - kafka集群就是多个broker

- **consumer** 消费者
  
  - 消费者组

- **offset**
  
  - 表示消费者消费进度，偏移量

- **Zookeeper**
  
  - 探测broker和consumer的添加或移除
  - 负责维护所有partition的领导者/从属者关系（主分区和备份分区），如果主分区挂了，需要选举出备份分区作为主分区。
  - 维护topic、partition等元配置信息

#### 二、基础

##### 1、获取topic列表

- bin/kafka-topics.sh --list --zookeeper localhost:2181

##### 2、**生产者在主题上发布消息**

- bin/kafka-console-producer.sh --broker-list 192.168.43.49:9092 --topicHello-Kafka

##### 3、**消费者接受消息**

- bin/kafka-console-consumer.sh --zookeeper localhost:2181 --topicHello-Kafka --from-beginning

#### 三、存在问题

##### 1、kafka 如何保证不丢消息

- **生产者丢失消息**
  
  - 调用send方法发送消息之后，网络丢失
    
    为了确定消息是发送成功，我们要判断消息发送的结果。send之后通过回调重试

- **消费者丢失消息**
  
  - 当消费者拉取到了分区的某个消息之后，消费者会自动提交了 offset。如果此时消费失败，即丢失消息。
    - 取消自动提交offset，真正消费完消息之后之后再自己手动提交 offset，但是可能会重复消费

- **kafka弄丢消息**
  
  - **leader 分区所在的 broker 突然挂掉，那么就要从 follower 副本重新选出一个 leader ，但是 leader 的数据还有一些没有被 follower 副本的同步的话，就会造成消息丢失。**
    
    - **设置 acks = all**  （所有副本都收到才判断发送成功）
      
      acks 的默认值即为1，代表我们的消息被leader副本接收之后就算被成功发送。当我们配置 **acks = all** 代表则所有副本都要接收到该消息之后该消息才算真正成功被发送。
    
    - **设置 replication.factor >= 3** （设置副本数）
    
    - **min.insync.replicas > 1**  （这样配置代表消息至少要被写入到 2 个副本才算是被成功发送）

##### 2、kafka 延迟队列
