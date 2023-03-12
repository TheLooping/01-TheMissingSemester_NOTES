###### 2023/03/06 周一 晚上06:59

重点是*正则表达式* （*regular expressions*）

## 以下面这段命令展开介绍
```shell
ssh myserver journalctl
 | grep sshd
 | grep "Disconnected from" 
 | sed -E 's/.*Disconnected from (invalid |authenticating )?user (.*) [^ ]+ port [0-9]+( \[preauth\])?$/\2/'
 | sort | uniq -c
 | sort -nk1,1 | tail -n10
 | awk '{print $2}' | paste -sd,
```

#### 1 ssh 远程连接服务器
```shell
ssh myserver journalctl
```

#### 2 grep 和 less(总命令没有使用)
```shell
ssh myserver 'journalctl | grep sshd | grep "Disconnected from"' | less
```

#### 3 sed (Stream Editor，流编辑器) ！！！                                     + 正则表达式

基本上给出了如何修改文件的简短命令，而不是而不是直接操作其内容。 有大量的命令，但最常见的命令之一是： ***替代***

#### sed

sed是一个流编辑器！什么是流编辑器？将文件或标准输入的内容作为编辑的对象，对其按照需求进行修改。

pattern space是什么？在流编辑中有pattern space的概念，翻译为：模式空间。简单理解就是一个缓冲区，sed从输入流中一行一行的取出内容放到这个缓冲区中按照SCRIPT（脚本）进行处理。

##### sed的总体结构

```shell
 sed [参数] '[定位][指令]' 处理的文本路径
 # 注：不指定定位，则默认处理全文。
```

###### 常用指令

`p` : 打印
`d` : 删除  
a : 在当前行后添加一行或多行内容  
c : 用新文件修改（替换）当前行中的文本  
i : 在当前行之前插入文本  
r ： 从以外文件中读相关内容，写到相关行之后  
w : 匹配到的行写入一个新的文件之中  
y ： 将字符转换成一个新的字符  
`s` : 用一个字符替整体替换成另外一个字符  *但是一般写在定位之前*
`g` : 全局执行  
`i` : 与s指令配合一起使用时，则是忽略大小写的作用

###### 参数

内部派系（POSIX & PCRE）以及POSIX标准（BRE & ERE）
-   不带参数，为POSIX BRE模式
-   带参数`-E`，为POSIX ERE模式，
-   带参数`-P`，为PCRE模式
![[Pasted image 20230307212939.png]]
`-E` 后`+?{}()` 均不需要[[Lec 4 Data Wrangling 数据整理#^0751b2|转义符]]

`PCRE`更加简洁方便：
	- 不需要使用\进行转义，1|2表达1或者2
	- 兼容POSIX字符组的基础上还支持更简洁的写法，如：`\w`等价于`[[:word:]]`(匹配一个单词)，`\d`等价于`[[:digit:]]`
	- 功能更加丰富（非捕获组，环顾断言，非贪婪）

详细对比见文末


`-E` : 支持扩展正则表达式，默认基础正则BRE（**sed中的正则表达式必须放在两个//中间**）  

`-e` : 允许多项编辑  
`-n` : 取消默认输出 ,通常与 指令 `p` 配合，只输出命令匹配行
`-i` : 就地编辑文本  



`-f`： 指定定位规则的文件
	`sed -f 2.txt 1.txt`
	其中2.txt内容是3d 4p
	即对1.txt进行 删除第三行，输出第四行操作


###### sed + 正则表达式（定位）


```shell
# 数字定位，输出第二行
sed -n '2p' 1.txt

# 范围定位，输出1-3行
sed -n '1,3p' 1.txt 
```

```shell
# 正则定位
# 正则表达式必须放在/ / 之间
# \作为转义符，/ /之间内容为 /sbin/nologin ,d是删除
sed -i -r "/\/sbin\/nologin/d" 3.txt 
# \c作为转义的代表，c可以替换为任意字符
sed -i -r "\c/sbin/nologincd" 3.txt
```

```shell
# ^a表示以a为行首，b$表示以b为行尾
# .匹配一次任意非行尾的字符
# a\？表示匹配0个或者1个a
# a\+ 表示匹配1个或多个a
# a*表示匹配任意多个a，也可以是0个 
sed  -r "/^ *#/d" /etc/nginx/nginx.conf
```

```shell
# [ab]可以匹配 a或b
# [^ab]表示否定ab，即匹配除ab以外的其他字符
# [a-c]表示范围匹配，即abc

# [0-9]{n}表示匹配重复n次的数字，即匹配一个长度为n的数字串
#      要注意{}的转义 \{0-9\}
sed -n '/^[0-9]\{3\}$/ p' numbers.txt 

# {n,}表示匹配长度>=n
# {m,n}表示匹配长度在 m n 之间

# |管道or逻辑或，但是要注意转义  输出为str1和str3两行
echo -e "str1\nstr2\nstr3\nstr4" | sed -n '/str\(1\|3\)/ p'
```

``` shell
# 转义符 \
# \n（换行），\r(回车)， \\(匹配\)
# \d97 (十进制97对应的ASCⅡ码，即a) 
# \o142 (8进制-b) ，\x63（16进制-c）
echo -e "a\nb\nc" | sed -n '/\d97/ p'





```

^0751b2

###### 例：替换

-   `s` 表示匹配替换，*通常搭配正则*

```shell
# pattern --> replacement
sed 's/pattern/replacement/flags' file
```
***falgs即指令***
	- 数字，表明新文本将替换第几处模式匹配的地方；
	- g，表明新文本将会替换所有匹配的文本；
	- p，表明原先行的内容要打印出来；
	- w, 将替换的结果写到文件中。



```shell
# 所有apple --> mango，输出到stdout 
command | sed 's/apple/mango/g'
command | sed -f path/to/script.sed
command | sed -E 's/(apple)/\U\1/g'    # 捕获
command | sed -n '1p'
```

-   `s` 表示匹配替换，*通常搭配正则*
-   `g` 一直替换 （如果没有，则只执行一次）
-   `i` 结果直接 修改 源文件
-   
###### 捕获
将Hello World替换成World Hello
```shell
# 被（ ）的内容可以被 \1 \2 \3 代替
sed -E "s/(Hello) (World)/\2 \1/g" 7.txt
```



#### sort 排序

```shell
# 排序 -n以数值排序，-r降序， k1,1
sort -nk1,1

# 第三列倒序，第二列正序
sort -n -t -k 3r -k 2 duweixin.net.txt
baidu 100 5000
google 110 5000
sohu 100 4500
guge 50 3000

# -k的全部 ，逗号是重点
[ FStart [ .CStart ] ] [ Modifier ] [ , [ FEnd [ .CEnd ] ][ Modifier ] ]

# 只针对公司英文名称的第二个字母进行排序，如果相同的按照员工工资进行降序排序
sort -t -k 1.2,1.2 -k 3,3nr duweixin.net.txt
baidu 100 5000
google 110 5000
sohu 100 4500
guge 50 3000
```

>由于只对第二个字母进行排序，所以我们使用了-k 1.2,1.2的表示方式，表示我们“只”对第二个字母进行排序。（如果你问“我使用-k 1.2怎么不行？”，当然不行，因为你省略了End部分，这就意味着你将对从第二个字母起到本域最后一个字符为止的字符串进行排序）。对于员工工资进行排 序，我们也使用了-k 3,3，这是*最准确*的表述，表示我们“只”对本域进行排序，因为如果你省略了后面的3，就变成了我们“对第3个域开始到最后一个域位置的内容进行排序” 了。[参考](https://www.jianshu.com/p/f218f98725a4)


###### uniq 检查重复项

```shell
# 删除文件中重复出现的行
# -c 在行首显示该行重复出现的次数
uniq -c testfile

# 当重复的行并不相邻时，uniq 命令是不起作用的
# 故配合 sort 使用
sort testfile1 | uniq -c
```


###### tail 尾部 head头部
```shell
# 后十行，可实时更新
tail -n10
```


#### awk 文本分析工具



#### paste 合并文件列
```shell
# 把所有列用，连接成一行
paste -sd,
```
-  `-s` 合并列
-  `-d`<间隔字符>用指定的间隔字符取代跳格字符


#### bc -l 数学计算
```shell
echo "2+5" | bc -l
```
- `-l` 数学库，没啥实际意义

#### xargs 输出-->参数
很多命令不支持|管道来传递参数，而日常工作中有有这个必要
```shell
find /sbin -perm +700 |ls -l       #这个命令是错误的
find /sbin -perm +700 |xargs ls -l   #这样才是正确的
```




#### 附表

![[Pasted image 20230307215114.png]]

![[Pasted image 20230307215131.png]]

![[Pasted image 20230307215204.png]]
