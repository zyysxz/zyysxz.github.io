---
layout: post
title:  "Apache Kafka Learning Notes"
date:   2022-05-09 10:00:00 +0800
author: Vito
categories: ["Kafka"]
---
## Topics，Partitions and offsets
1. offset only have a meaning in a specific partition
2. offset can be regarded as ordered only in a specific partition
3. offset will not be re-used within a partition even if the former data were deleted
4. data is assigned randomly to a partition unless a key is providered(if you want to fetch ordered msg, add a key or use only one partition - not recommended)

## Producers and Mssage Key
1. the producer will decide which partition the message will be sent to.
2. messages sharing the same key will always be sent to the same partition
3. Message Structure
![msg structure](http://raw.githubusercontent.com/zyysxz/zyysxz.github.io/main/_posts/pictures/kafka_msg.png)
4. key hashing is using murmur2 algorithm by defaut

## Consumers and Desirialization
1. Unlike Rabbitmq and some other message midware, Kafka is using pull model which is one of the main reason that Kafka have high performance.
2. Consumers will automatically know which partition to read from

    **<font color="red">Question</font>** - will a certain consumer be assigned to read from a certain partitions, and remain unchanged until it reboot? If yes, how did these instances be assigned?

    Each consumer within consumer groups reads from exclusive partitions
    ![consumer group](https://raw.githubusercontent.com/zyysxz/zyysxz.github.io/main/_posts/pictures/Consumer%20group.png)
3. Serialization and Desirialization type can not be changed during the lifecycle of the topic, if you want to change, create a new topic.

## Consumer Groups and Consumer Offsets
1. if there are more consumers within one consumer group than partitions, the rest part of consumers will stay inactive. For example, if we have 3 partitions and three instances within one group, apparently, each instance will take over one partition, but if we have 4 instances instead of 3, the fourth instance will not help instance 1 or 2 or 3, while it will stay inactive, you can think about why kafka will have this mechanism.
2. kafka store offsets of a consumer group, when a consuemr in a group has processed data received from Kafka, it should be periodically commiting the offsets (the Kafka broker will write to consumer_offsets, not the group itself), because consumer instance is a client, if it fails and other instance take over its position, it should continue from the offset that already being processed.
3. 3 delivery semantics
    * at least once
    * at most once
    * exact once

## Brokers and Topics
1. Kafka broker structure within a cluster
![Kafka broker structure](https://raw.githubusercontent.com/zyysxz/zyysxz.github.io/main/_posts/pictures/Consumer%20group.png)
2. Kafka topic replication factor
more than 1 in production env, usually 3








## Appendix
Answer 1: if there are two instances to take care of the msgs within one partition, it will be difficult to consume the messages in order.