# grep和find的区别
grep搜索的是文本，即文本中有哪些字符等，grep是包含匹配(正则表达式)

find搜索的是文件，即某个文件夹中有哪些文件，find的完全匹配(通配符)

# 命令使用
## find
查找文件或目录

语法: find [查找位置] [文件名或目录名]

例如：在/data 目录下查找index.html
```
find /root index.html
```

在当前目录及其子目录中查找任何扩展名为“c”的文件
```
1、find . -name "*.c"
2、find bootable kernel -name "*.c"
```

## grep
在文件中查找字符串

语法: grep [字符串] [文件名]

例如：在文件test.txt中查找sucess
```
grep success test.txt
```

查找/etc/test下所有包含字符update的文件，并打印出该字符串所在行内容
```
grep -r update /etc/test
```

## grep和find结合

在当前目录下的makefile文件中搜索test.a这个词
```
find . -name "makefile" -print | xargs grep "test.a"
```

