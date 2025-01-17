## 传统行业 OA ERP

单点系统

业务逻辑层 数据库层 控制层 全部在一个项目中

## 互联网公司 	

分布式架构 	微服务架构

用户量大  

### 高并发  集群 负载均衡

### 高可用

冗余+自动故障转移

高可用（High Availability）计算机系统的可用性用 [1]  [平均无故障时间](https://baike.baidu.com/item/平均无故障时间)（MTTF）来度量

单点是系统高可用的大敌	高可用保证的原则是“集群化”，或者叫“冗余”

有冗余备份，挂了还有其他backup能够顶上

“自动故障转移”来实现系统的高可用

为什么使用分布式架构 微服务架构?

项目庞大 需要拆分解耦合

设计 dubbo Zookeeper SpringCloud SpringBoot

SpringBoot 默认依赖的web 组件 SpringMVC

# SOA

（面向服务的架构）

面向服务的架构（SOA）是一个组件模型，它将应用程序的不同功能单元（称为服务）进行拆分，并通过这些服务之间定义良好的接口和契约联系起来。

接口是采用中立的方式进行定义的，它应该独立于实现服务的硬件平台、操作系统和编程语言。这使得构建在各种各样的系统中的服务可以以一种统一和通用的方式进行交互。



### 和微服务区别:













一、什么是高可用

高可用HA（High Availability）是分布式系统架构设计中必须考虑的因素之一，它通常是指，通过设计减少系统不能提供服务的时间。

假设系统一直能够提供服务，我们说系统的可用性是100%。

如果系统每运行100个时间单位，会有1个时间单位无法提供服务，我们说系统的可用性是99%。

很多公司的高可用目标是4个9，也就是99.99%，这就意味着，系统的年停机时间为8.76个小时。

百度的搜索首页，是业内公认高可用保障非常出色的系统，甚至人们会通过www.baidu.com 能不能访问来判断“网络的连通性”，百度高可用的服务让人留下啦“网络通畅，百度就能访问”，“百度打不开，应该是网络连不上”的印象，这其实是对百度HA最高的褒奖。

 

二、如何保障系统的高可用

我们都知道，单点是系统高可用的大敌，单点往往是系统高可用最大的风险和敌人，应该尽量在系统设计的过程中避免单点。方法论上，高可用保证的原则是“集群化”，或者叫“冗余”：只有一个单点，挂了服务会受影响；如果有冗余备份，挂了还有其他backup能够顶上。

保证系统高可用，架构设计的核心准则是：冗余。

有了冗余之后，还不够，每次出现故障需要人工介入恢复势必会增加系统的不可服务实践。所以，又往往是通过“自动故障转移”来实现系统的高可用。

接下来我们看下典型互联网架构中，如何通过冗余+自动故障转移来保证系统的高可用特性。

 

三、常见的互联网分层架构

![img](http://mmbiz.qpic.cn/mmbiz_png/YrezxckhYOxZeXfbW7Kckceh9CscyyfG8PWEuTHibe1esjn7luyCtazdzwCtVOXooa4qzVqkEbLADEnJ7vaJqRQ/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1)
常见互联网分布式架构如上，分为：

（1）客户端层：典型调用方是浏览器browser或者手机应用APP

（2）反向代理层：系统入口，反向代理

（3）站点应用层：实现核心应用逻辑，返回html或者json

（4）服务层：如果实现了服务化，就有这一层

（5）数据-缓存层：缓存加速访问存储

（6）数据-数据库层：数据库固化数据存储

整个系统的高可用，又是通过每一层的冗余+自动故障转移来综合实现的。

 

四、分层高可用架构实践

【客户端层->反向代理层】的高可用

![img](http://mmbiz.qpic.cn/mmbiz_png/YrezxckhYOxZeXfbW7Kckceh9CscyyfGW76dBwjO10j7aKNOz3gud6smtCGczwsLhoe6KAOsGEL68dfqczVtMQ/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1)
【客户端层】到【反向代理层】的高可用，是通过反向代理层的冗余来实现的。以nginx为例：有两台nginx，一台对线上提供服务，另一台冗余以保证高可用，常见的实践是keepalived存活探测，相同virtual IP提供服务。

 

![img](http://mmbiz.qpic.cn/mmbiz_png/YrezxckhYOxZeXfbW7Kckceh9CscyyfGasDdhCicBUbqDEgaibkRmD6jOUlziaCcSpm9Fm16mncWibPjZN1bdNvUhw/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1)
自动故障转移：当nginx挂了的时候，keepalived能够探测到，会自动的进行故障转移，将流量自动迁移到shadow-nginx，由于使用的是相同的virtual IP，这个切换过程对调用方是透明的。

 

【反向代理层->站点层】的高可用

![img](http://mmbiz.qpic.cn/mmbiz_png/YrezxckhYOxZeXfbW7Kckceh9CscyyfGxZwNZAVYBYMZ0kx5CL8Xd5licNL0LRaNhr6P1wOu7WmJgqSOsciaicHow/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1)
【反向代理层】到【站点层】的高可用，是通过站点层的冗余来实现的。假设反向代理层是nginx，nginx.conf里能够配置多个web后端，并且nginx能够探测到多个后端的存活性。

 

![img](http://mmbiz.qpic.cn/mmbiz_png/YrezxckhYOxZeXfbW7Kckceh9CscyyfGMchdES6v0NmZckrIh9LYU6PkcA7KIokib55FcwgMN4iajr2gMbJWic7jQ/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1)
自动故障转移：当web-server挂了的时候，nginx能够探测到，会自动的进行故障转移，将流量自动迁移到其他的web-server，整个过程由nginx自动完成，对调用方是透明的。

 

【站点层->服务层】的高可用

![img](http://mmbiz.qpic.cn/mmbiz_png/YrezxckhYOxZeXfbW7Kckceh9CscyyfG1iaQwN54JCv5RYC21LlD83CFDxJbicLv9TJGGbeG9E6BicdZs9NPI610Q/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1)
【站点层】到【服务层】的高可用，是通过服务层的冗余来实现的。“服务连接池”会建立与下游服务多个连接，每次请求会“随机”选取连接来访问下游服务。

 

![img](http://mmbiz.qpic.cn/mmbiz_png/YrezxckhYOxZeXfbW7Kckceh9CscyyfGcrpCknA0M0eXVk9HjnHWA0OKSK1nWMHTjq7ocmYT5MWLq9VRluowgg/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1)
自动故障转移：当service挂了的时候，service-connection-pool能够探测到，会自动的进行故障转移，将流量自动迁移到其他的service，整个过程由连接池自动完成，对调用方是透明的（所以说RPC-client中的服务连接池是很重要的基础组件）。

 

【服务层>缓存层】的高可用

![img](http://mmbiz.qpic.cn/mmbiz_png/YrezxckhYOxZeXfbW7Kckceh9CscyyfGZ4Y7O6EriaRIBJEHrewIEickM6zjcwmRGnuUUvYtvMe3kP8GZOMp7sjg/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1)
【服务层】到【缓存层】的高可用，是通过缓存数据的冗余来实现的。

缓存层的数据冗余又有几种方式：第一种是利用客户端的封装，service对cache进行双读或者双写。

 

![img](http://mmbiz.qpic.cn/mmbiz_png/YrezxckhYOxZeXfbW7Kckceh9CscyyfGaaRmZHrjuhjicWbicibDOrsjjHvsE85JiaiciaTur2ggCY96OPQkyvZyXicbg/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1)
缓存层也可以通过支持主从同步的缓存集群来解决缓存层的高可用问题。

以redis为例，redis天然支持主从同步，redis官方也有sentinel哨兵机制，来做redis的存活性检测。

 

![img](http://mmbiz.qpic.cn/mmbiz_png/YrezxckhYOxZeXfbW7Kckceh9CscyyfGdiaPuQmUGr1JqaQ4MsPibP8UjzlXibaVIk6W9oSDFVcmK31ibGTibPF6dicw/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1)
自动故障转移：当redis主挂了的时候，sentinel能够探测到，会通知调用方访问新的redis，整个过程由sentinel和redis集群配合完成，对调用方是透明的。

 

说完缓存的高可用，这里要多说一句，业务对缓存并不一定有“高可用”要求，更多的对缓存的使用场景，是用来“加速数据访问”：把一部分数据放到缓存里，如果缓存挂了或者缓存没有命中，是可以去后端的数据库中再取数据的。

这类允许“cache miss”的业务场景，缓存架构的建议是：

![img](http://mmbiz.qpic.cn/mmbiz_png/YrezxckhYOxZeXfbW7Kckceh9CscyyfG6ibqmZXiclFlLAz7KcHt0iaKNOkoib7eYkJLxIKia4YicoI2ZaZN2Ts2cUWw/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1)
将kv缓存封装成服务集群，上游设置一个代理（代理可以用集群冗余的方式保证高可用），代理的后端根据缓存访问的key水平切分成若干个实例，每个实例的访问并不做高可用。

 

![img](http://mmbiz.qpic.cn/mmbiz_png/YrezxckhYOxZeXfbW7Kckceh9CscyyfGmGgPtUdTY7MTwVicS3kBMWcvdMCGrZeJM8ibk3cV7TY5R9BXic1pP59LQ/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1)
缓存实例挂了屏蔽：当有水平切分的实例挂掉时，代理层直接返回cache miss，此时缓存挂掉对调用方也是透明的。key水平切分实例减少，不建议做re-hash，这样容易引发缓存数据的不一致。

 

【服务层>数据库层】的高可用

大部分互联网技术，数据库层都用了“主从同步，读写分离”架构，所以数据库层的高可用，又分为“读库高可用”与“写库高可用”两类。

 

【服务层>数据库层“读”】的高可用

![img](http://mmbiz.qpic.cn/mmbiz_png/YrezxckhYOxZeXfbW7Kckceh9CscyyfGvlyLhdDQprCwKM09642AvxZUzvZxRbLg3vwtAeeSNQ2kVhs1eA1SrA/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1)
【服务层】到【数据库读】的高可用，是通过读库的冗余来实现的。

既然冗余了读库，一般来说就至少有2个从库，“数据库连接池”会建立与读库多个连接，每次请求会路由到这些读库。

 

![img](http://mmbiz.qpic.cn/mmbiz_png/YrezxckhYOxZeXfbW7Kckceh9CscyyfGR4X9glYtU23ThQ251I6Xb6hHDLASbYzlupexhhjmjAf8ib8NnNibvu8g/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1)
自动故障转移：当读库挂了的时候，db-connection-pool能够探测到，会自动的进行故障转移，将流量自动迁移到其他的读库，整个过程由连接池自动完成，对调用方是透明的（所以说DAO中的数据库连接池是很重要的基础组件）。

 

【服务层>数据库层“写”】的高可用

![img](http://mmbiz.qpic.cn/mmbiz_png/YrezxckhYOxZeXfbW7Kckceh9CscyyfGEEupewicyaRavGSO1GbNbe8h78g7jOwnuAbHWKJSsNZCAWMZeqia4dZw/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1)
【服务层】到【数据库写】的高可用，是通过写库的冗余来实现的。

以mysql为例，可以设置两个mysql双主同步，一台对线上提供服务，另一台冗余以保证高可用，常见的实践是keepalived存活探测，相同virtual IP提供服务。

 

![img](http://mmbiz.qpic.cn/mmbiz_png/YrezxckhYOxZeXfbW7Kckceh9CscyyfG9tWoA9eLB2pUeBkeq7VS3BneDkfbZoxGqibeDud4YslUl53dInkNMjw/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1)
自动故障转移：当写库挂了的时候，keepalived能够探测到，会自动的进行故障转移，将流量自动迁移到shadow-db-master，由于使用的是相同的virtual IP，这个切换过程对调用方是透明的。

 

五、总结

高可用HA（High Availability）是分布式系统架构设计中必须考虑的因素之一，它通常是指，通过设计减少系统不能提供服务的时间。

方法论上，高可用是通过冗余+自动故障转移来实现的。

整个互联网分层系统架构的高可用，又是通过每一层的冗余+自动故障转移来综合实现的，具体的：

（1）【客户端层】到【反向代理层】的高可用，是通过反向代理层的冗余实现的，常见实践是keepalived + virtual IP自动故障转移

（2）【反向代理层】到【站点层】的高可用，是通过站点层的冗余实现的，常见实践是nginx与web-server之间的存活性探测与自动故障转移

（3）【站点层】到【服务层】的高可用，是通过服务层的冗余实现的，常见实践是通过service-connection-pool来保证自动故障转移

（4）【服务层】到【缓存层】的高可用，是通过缓存数据的冗余实现的，常见实践是缓存客户端双读双写，或者利用缓存集群的主从数据同步与sentinel保活与自动故障转移；更多的业务场景，对缓存没有高可用要求，可以使用缓存服务化来对调用方屏蔽底层复杂性

（5）【服务层】到【数据库“读”】的高可用，是通过读库的冗余实现的，常见实践是通过db-connection-pool来保证自动故障转移

（6）【服务层】到【数据库“写”】的高可用，是通过写库的冗余实现的，常见实践是keepalived + virtual IP自动故障转移