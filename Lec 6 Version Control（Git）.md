###### 2023/03/11 周六 晚上06:54

- 用于跟踪源代码更改的工具
- 用一系列快照中跟踪，每个快照将整个状态存放在顶级目录内
- 维护元数据，如快照创建者、与每个快照关联的信息等

作用
- 记录修改原因
- 解决合作中的问题

现代 VCS 还可让您轻松（通常自动）回答问题 喜欢：
-   谁写了这个模块？
-   这个特定文件的这一特定行是什么时候编辑的？由谁？为什么 是编辑过的吗？
-   在过去的 1000 次修订中，特定单元测试何时/为何停止 加工？


git的交互丑陋。。。但其底层设计很好
bottom-up更易于理解

数据模型-->命令行界面

## 1 Git 的数据模型

#### 1.1 快照

在git中，目录被称之为`tree` ,文件被称之为`blob` (只是一堆字节)
```txt
<root> (tree)
|
+- foo (tree)
|  |
|  + bar.txt (blob, contents = "hello world")
|
+- baz.txt (blob, contents = "git is wonderful")
```



#### 1.2 

![](attachment/Pasted%20image%2020230312222832%201.png)


## [[Lec 6+Gitkraken]]





[Lec 6 Version Control（Git）](Lec%206%20Version%20Control（Git）.md)

