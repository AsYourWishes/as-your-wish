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

