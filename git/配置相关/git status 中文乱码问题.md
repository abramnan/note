当我们使用git status 时 会遇到中文乱码的问题 如下
``` shell
	deleted:    "\345\200\222\346\216\222\347\264\242\345\274\225.md"
	deleted:    "\346\255\243\346\216\222\347\264\242\345\274\225.md"
	deleted:    "\346\255\243\346\216\222\347\264\242\345\274\225\345\233\276.md"
	deleted:    "\351\273\204\351\207\221\345\234\210\346\263\225\345\210\231.md"
```

解决办法：执行下面的命令即可
``` shell
// 这个命令就可以让git status的中文文件名正常显示了
git config --global core.quotepath false # 显示 status 编码
```

下面的命令不是针对git status中文乱码的
```shell
$ git config --global gui.encoding utf-8            # 图形界面编码
$ git config --global i18n.commit.encoding utf-8    # 提交信息编码
$ git config --global i18n.logoutputencoding utf-8  # 输出 log 编码
$ export LESSCHARSET=utf-8
# 最后一条命令是因为 git log 默认使用 less 分页，所以需要 bash 对 less 命令进行 utf-8 编码
```
