###### 2023/03/10 周五 下午03:03

## 1 Job Control

Ctrl + C :即向shell发送一个*SIGINT* 的signal

```shell
man signal 
cman signal #查看中文版
tldr kill 
kill -l# 查看信号名字及其序号（没有解释）
```
![[Pasted image 20230311122216.png]]

![[Pasted image 20230311122856.png]]


```shell
jobs # 查看当前所有进程
nohup sleep 2000 &# 后台运行

sleep 1000
^Z# STOP
bg %2 #sleep 转到后台开始运行
kill -9 %{1,2}# kill掉1 2号进程（sleep）这里是通配符，不同于正则

```


## 2 Terminal Multiplexers -- tmux

*终端多路复用*
sessions 任务，会话
	windows 窗口
		tabs 标签

`tmux`的键绑定，过程是`<C-b> x`
按下`<C-b>` ，释放 ，按下 `x`


### tumx改键重新映射
`<C-b>` --> `<C-a>`
修改配置文件 `~/.tmux.conf`
```shell
#将激活控制台的快捷键由Ctrl+b修改为Ctrl+a 
set-option -g prefix C-a
unbind-key C-b
bind-key C-a send-prefix
```


#### 2.1 sessions 多窗口独立工作区

进入session前
-   `tmux`启动新会话。
-   `tmux new -s NAME`以该名称开头。
-   `tmux ls`列出当前会话
-   `tmux a`附加最后一个会话。
-   `tmux a -t name` 重新回到指定会话（name是session名字）
进入session后
-   在键入中分离当前会话`tmux` `<C-b> d` 退回后台
-   `<C-$>` 重命名当前session
-   ***退出***会话/窗口/标签`<C-d>`

#### 2.2 windows 同意会话内多个选项卡窗口

-   `<C-b> c`创建新窗口。要关闭它，您只需终止外壳即可`<C-d>`
-   `<C-b> N`转到第 _N_ 个窗口。请注意，它们是有编号的
-   `<C-b> p`转到上一个窗口
-   `<C-b> n`转到下一个窗口
-   `<C-b> ,`重命名当前窗口
-   `<C-b> w`列出当前窗口

#### 2.3 tabs 同一window内 水平/垂直 拆分

-   `<C-b> "`水平拆分当前窗格
-   `<C-b> %`垂直拆分当前窗格
-   `<C-b> <direction>`按指定_方向_移动到窗格。这里的方向是指方向键。
-   `<C-b> z`切换当前窗格的缩放
-   `<C-b> <space>`循环浏览窗格排列。

## 2.5 Aliases别名

```shell
alias ll="ls -lh"
\ls # To ignore \ 转义符恢复原意
unalias ll # 解绑
alias ll # 打印定义即 ls -lh
```

## 3 Dot files

许多程序是使用称为*点文件*的纯文本文件配置的（文件名开头， 默认情况下隐藏在目录列表中）。`~/.vimrc` 是vim的配置文件

通过点文件配置的其他一些工具示例包括：
-   `bash` - `~/.bashrc`,`~/.bash_profile`
-   `git` - `~/.gitconfig`
-   `vim` - `~/.vimrc`和文件夹`~/.vim`
-   `ssh` - `~/.ssh/config`
-   `tmux` - `~/.tmux.conf`

#### rcm管理配置文件

把所有的dotfile统一管理
[链接](http://t.csdn.cn/m0gcb)

## 4 Remote Machine 远程ssh

```shell
ssh foo@IP # 以用户foo远程登陆相应IP的服务器
ssh foo@bar.mit.edu # 域名也可
```

#### 我用了finalshell
操作方便

注意：.NAT模式和桥接模式区别
![[Pasted image 20230311182639.png]]

.NAT模式下
虚拟机可以ping通宿主机，*反之不能*

