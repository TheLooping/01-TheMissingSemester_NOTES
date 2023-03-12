###### 2023/03/05 周日 下午05:56

## 1 命令

#### 1.1 which 用作查找$PATH中的命令
```shell
echo $PATH
which echo  
```

#### 1.2 重定向 < > >>
流输入输出，保存到一个临时文件中
- 覆盖写入 >file 
- 追加写入>>
- 读取 <

```shell
echo hello > test.txt
cat < text.txt
```

#### 1.3 管道 |
前一个程序的输出作为后一个程序的输入
```shell
ls -l | tail -n1
```


## 2 工具

#### 2.1权限sudo
- 系统管理员 `#`
- 非系统管理员 `$`
- 当前用户home `~`

- 管道在传输后权限发生变化
	```shell
	sudo echo 3 | brightness
	An error...
	echo 3 | sudo tee brightness
	```


