# Linux命令记录

## 1. 动态地显示时间

```shell
watch -n 1 date
# 如果date后有参数要把date和参数用双引号引起来
watch -n 1 "date +%T"

# 脚本
#!/bin/bash

while [ 1 ]
do
    printf '\b\b\b\b\b\b\b\b%s' `date +%T`
    sleep 1
done
```

### 2.nohup重定向到其它的日志文件

语法：nohup Command [ Arg … ] [　& ]

&：是指在后台运行，当用户退出（挂起）的时候，命令自动跟着结束

nohup：不挂断的运行，注意并没有后台运行的功能，就是指用nohup运行命令可以使命令永久的执行下去，和用户终端没有关系，例如我们断开SSH连接都不会影响他的运行，注意了nohup没有后台运行的意思；&才是后台运行因此将nohup和&结合使用，就可以实现使命令永久地在后台执行的功能

```shell
nohup command > myout.file 2>&1 &
```

2 >&1就是把stderr重定向到stdout，由于stdout被重定向到 console.txt，所以最终stderr、stdout均被重定向到console.txt并且解决了两者缓冲区不一致的问题。