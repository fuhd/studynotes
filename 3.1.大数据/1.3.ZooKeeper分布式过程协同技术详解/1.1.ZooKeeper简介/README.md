Zookeeper简介
================================================================================
**如何让一个应用中多个独立的程序协同工作是一件非常困难的事情**。开发这样的应用，很容易让很多开发
人员陷入如何使多个程序协同工作的逻辑中，最后导致没有时间更好地思考和实现他们自己的应用程序逻辑。

**Zookeeper从文件系统API得到启发，提供一组简单的API，使得开发人员可以实现通用的协作任务，包括
选举主节点、管理组内成员关系、管理元数据等。Zookeeper包括一个应用开发库（主要提供Java和C两种语言
的API）和一个用Java实现的服务组件。Zookeeper的服务组件运行在一组专用服务器之上，保证了高容错性
和可扩展性**。

**当你决定使用Zookeeper来设计应用时，最好将应用数据和协同数据独立开**。



