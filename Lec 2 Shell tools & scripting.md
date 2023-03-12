###### 2023/03/05 周日 下午05:58

## 1 scripting

#### 1.1 赋值变量 =

- **不能有空格**
- 引用时要用 `$`
- 双引号内引用，单引号输出变量名
```shell
foo=bar
echo "$foo"
# print bar
echo '$foo'
# print foo
```

#### 1.2 脚本函数示例

```shell
#! usr/bin/sh  # shebang 指出解释器绝对路径
mcd ( ){
		mkdir -p "$1"
		cd "$1"
}
```

#### 1.3 $+参数

-   `$0`- 脚本名称
-   `$1` - 脚本的参数。 是第一个参数，依此类推。`$9``$1`
-   `$@`- 所有参数，可用于for循环
	```shell
	for file in "$@"; do
		# ...
	done
```
-   `$#`- 参数数量
-   `$?`- 返回上一个命令的结果，比如 *return 0* 里的 *0*
-   `$$`- 当前脚本的进程标识号 *`PID`*
-   `!!`- 整个最后一个命令，包括参数。一种常见的模式是仅执行命令，使其由于缺少权限而失败;你可以通过 sudo 快速重新执行命令`sudo !!`
-   `$_`- 最后一个命令的最后一个参数。如果您在交互式 shell 中，还可以通过键入后跟 or 来快速获取此值

#### 1.4 关系运算符 || &&

优化语句
```shell
# 当第一个条件为假时，要继续执行，故输出提示
# 当第一个条件为真时，则不输出
false || echo "Oops, fail!" # 此时为假，输出提示
true || echo "will not be printed" # 此时真，不输出
```

`&&`和`;`不做赘述

#### 1.5 通配符 `*`  `?` 和大括号 { }

并不同于[[Lec 4 Data Wrangling 数据整理#sed + 正则表达式（定位）|正则表达]]
- 替换作用，模糊查找  注意`？`只能代替一个字符

- `{ }`可以简写很多东西
```shell
touch home/Downloads/{foo,bar}/demo{1..5}.{txt,py}
# 一下就创建了很多 txt和py文件

diff <(ls foo) <(ls bar)
# 比较两个临时文档的不同，相同的不输出
```
 
## 2 tools

#### 2.1 帮助 man tldr
```shell
man echo
tldr echo # too lang! didn't read
```

#### 2.2 查找find

不记得就用 tldr find
```shell
tldr find
# 例如查找所有上级目录叫test的python文件
find . -path '*/test/*.py' -type f
```

#### 2.3 内容查找 grep 或者rg

结构
```shell
grep [options] [-e pattern] [file...]
grep -i "string" filename 
```
- `-i` 忽略大小写
- `-n` 显示匹配的行号
- `-c` **只输出**匹配的行数
- `-r` 递归搜索目录
- `-A3` 显示匹配行+*前*三行
- `-B3` 显示匹配行+*后*三行
- `-C3` 匹配行+前三行+后三行

- `-e` 启用正则表达式 用的不多，以后再说

#### 2.4 fish基于历史命令的自动匹配

最开始首先输入 `fish`

#### 2.5 比tree更舒服的`broot`

```shell
broot [dir]
```
