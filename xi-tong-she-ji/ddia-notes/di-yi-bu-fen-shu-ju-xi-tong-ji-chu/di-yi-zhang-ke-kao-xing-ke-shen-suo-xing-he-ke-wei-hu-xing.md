# 第一章：可靠性、可伸缩性和可维护性

*   可靠性（Reliability）

    系统在 **困境**（adversity，比如硬件故障、软件故障、人为错误）中仍可正常工作（正确完成功能，并能达到期望的性能水准）。
*   可伸缩性（Scalability）

    有合理的办法应对系统的增长（数据量、流量、复杂性）。
*   可维护性（Maintainability）

    许多不同的人（工程师、运维）在不同的生命周期，都能高效地在系统上工作（使系统保持现有行为，并适应新的应用场景）。

## 可靠性

造成错误的原因叫做 **故障（fault）**，能预料并应对故障的系统特性可称为 **容错（fault-tolerant）** 或 **韧性（resilient）**。尽管比起 **阻止错误（prevent error）**，我们通常更倾向于 **容忍错误**。但也有 **预防胜于治疗** 的情况（比如不存在治疗方法时）。

* **硬件故障**：为了减少系统的故障率，可以增加单个硬件的冗余度，目前来说，很少有单个机器完全失效，同时越来越多的应用也开始使用大量机器，而不是依赖单机可靠性。
* **软件错误**：内部的系统性错误，需要系统在运行中不断自检，并在出现 **差异（discrepancy）** 时报警。
* **人为错误**：如何设计系统？解耦，沙箱环境，corner case等等

## 可伸缩性 <a href="#ying-jian-gu-zhang" id="ying-jian-gu-zhang"></a>

**可伸缩性（Scalability）** 是用来描述系统应对负载增长能力的术语。

* **描述负载**：负载可以用一些称为 **负载参数（load parameters）** 的数字来描述。参数的最佳选择取决于系统架构，它可能是每秒向 Web 服务器发出的请求、数据库中的读写比率、聊天室中同时活跃的用户数量、缓存命中率或其他东西。
* **描述性能**：对于 Hadoop 这样的批处理系统，通常关心的是 **吞吐量（throughput）**，即每秒可以处理的记录数量，或者在特定规模数据集上运行作业的总时间 \[^iii]。对于在线系统，通常更重要的是服务的 **响应时间（response time）**，即客户端发送请求到接收响应之间的时间。
