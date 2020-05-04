## 任务

任务其实就是指某一次抓取任务或采集任务。任务与爬虫关联，其执行的也是爬虫指定的执行命令或采集规则。抓取或采集的结果与任务关联，因此可以查看到每一次任务的结果集。Crawlab的任务是整个采集流程的核心，抓取的过程都是跟任务关联起来的，因此任务对于Crawlab来说非常重要。任务被`主节点`触发，`工作节点`通过任务队列接收任务，然后在其所在节点上执行任务。

本小节将介绍以下内容：
1. [运行任务](./Run.md)
2. [任务日志](./Log.md)
3. [任务结果](./Results.md)
4. [操作任务](./Action.md)