---
title: Akka Doc
date: 2019-08-25 13:54:43
categories:
- Akka
tags:
- Akka
---
## Akka继承体系
- 通过context.actorOf()创建一个actor的引用
- 用system.actorOf(…, "someActor")来创建actor的话，创建出来的actor引用的路径将包含/user/someActor
- akka默认创建了三个actor，root guardian、user guardian、system guardian

root guardian:系统中所有actor的父级，也是系统结束时最后一个停止的actor
user guradian:所有用户创建的actor的父级

## The actor lifecycle
- preStart() is invoked after the actor has started but before it processes its first message.
- postStop() is invoked just before the actor stops. No messages are processed after this point.

## Failure handling
- Parent actor和Child actor在他们的生命周期中是相互联系的。每当参与者失败(抛出异常或未处理的异常从receive中冒泡出来)，它就会暂时挂起。如前所述，故障信息被传播到父对象，然后父对象决定如何处理子参与者引起的异常。这样，父母就成了孩子的监督者。默认的管理器策略是停止并重新启动子进程。如果不更改默认策略，所有失败都会导致重新启动。

## Message delivery
1.  At-most-once delivery — each message is delivered zero or one time; in more causal terms it means that messages can be lost, but are never duplicated.
    - 开销最小，性能最高，因为它可以以“即发即弃”的方式完成，而无需将状态保存在发送端或传输机制中。
2.  At-least-once delivery — potentially multiple attempts are made to deliver each message, until at least one succeeds; again, in more causal terms this means that messages can be duplicated but are never lost.
    - 增加了在发送端保持状态和在接收端拥有确认机制的开销。
3.  Exactly-once delivery — each message is delivered exactly once to the recipient; the message can neither be lost nor be duplicated.
    - 开销最大，性能最差，除了来自至少一次发送消息的开销之外，还需要在接收端保持状态以确认消息是否重复。