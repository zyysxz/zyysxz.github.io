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
3. Serialization and Desirialization type can not be changed during the lifecycle of the topic, if you want to change, create a new topic.