# 定义
AWK是一种处理文本文件的语言，是一个强大的文本分析工具

# 命令
```
awk [选项参数] 'script' var=value file(s)  # script是指用''扩起来的需要执行的脚本
或
awk [选项参数] -f scriptfile var=value file(s) # 与上面的区别就是这里的脚本是通过读取脚本文件执行的，适应脚本过长的情况
```

# 基本用法
log.txt文本内容：
```
2 this is a test
3 Do you like awk
This's a test
10 There are orange,apple,mongo
```

## 用法一
```
awk `{[pattern] action}` {filenames}  # 行匹配语句 awk '' 只能用单引号
```

实例
```
# 每行按空格或tab健分割，输出文本中的第一和第四项
awk '{print $1,$4}' log.txt
# 输出
2 a
3 like
this's
10 There are orange,apple,mongo
```

```
# 格式化输出
awk '{printf "%-8s %-10s\n",$1,$4}' log.txt
# 输出
2        a         
3        like      
This's             
10       orange,apple,mongo
```

## 用法二
```
awk -F # -F相当于内置变量FS(field separator)，指定分割字符
```

实例
```
# 使用","分割
awk -F, '{print $1,$2}' log.txt
# 输出
2 this is a test
3 Do you like awk
This's a test
10 There are orange apple

# 或者使用内建变量
awk 'BEGIN{FS=","} {print $1,$2}' log.txt
# 输出
2 this is a test
3 Do you like awk
This's a test
10 There are orange apple

# 使用多个分隔符.先使用空格分割，然后对分割结果再使用","分割
awk -F '[ ,]' '{print $1,$2,$5}' log.txt
# 输出
2 this test
3 Are awk
This's a
10 There apple
```

## 用法三
```
awk -v  # 设置变量
```

实例
```
awk -va=1 '{print $1,$1+a}' log.txt
# 输出
2 3
3 4
This's 1
10 11

awk -va=1 -vb=s '{print $1,$1+a,$1b}' log.txt
# 输出
2 3 2s
3 4 3s
This's 1 This'ss
10 11 10s
```

## 用法四
```
awk -f {awk脚本} {文件名}
```

实例
```
awk -f cal.awk log.txt
```

## 工作样例
```
grep -oP 'P=\w+,' | awk -F= '{print $2}' | awk -F, '{print $1}' | sort | uniq -c
```