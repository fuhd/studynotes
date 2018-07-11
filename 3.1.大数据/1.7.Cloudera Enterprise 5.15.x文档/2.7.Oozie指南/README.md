Oozie指南
================================================================================
适用于Hadoop的Apache Oozie Workflow Scheduler是一个用于管理Apache Hadoop作业的工作流和
调度服务：
+ Oozie Workflow作业是动作的有向无环图（DAG）；动作通常是Hadoop作业（MapReduce，Streaming，
Pipes，Pig，Hive，Sqoop等）。
+ Oozie Coordinator作业根据时间（频率）和数据可用性触发重复的工作流作业。
+ Oozie Bundle作业是作为单个作业管理的Coordinator作业集。

Oozie是一种可扩展、可伸缩和数据感知的服务，可用于协调在Hadoop上运行的作业之间的依赖关系。
