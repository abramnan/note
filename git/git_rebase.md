# git rebase
## 合并多个commit
### 合并步骤
1、git log查看当前commit记录
2、git rebase -i [commitid]
* -i :弹出交互式界面进行编辑合并
* commitid ： 要合并多个版本之前的版本号，commitid本身不参与合并

3、编辑交互界面
- 命令解释
-   p, pick = use commit
-   r, reword = use commit, but edit the commit message
-   e, edit = use commit, but stop for amending
-   s, squash = use commit, but meld into previous commit
-   f, fixup = like "squash", but discard this commit's log message
-   x, exec = run command (the rest of the line) using shell
-   d, drop = remove commit

4、git push -f XXX分支

## git 合并其他分支
1、git rebase master (这里以当前分之合并master分支为例)
2、编辑代码，解决冲突
3、git add
4、git rebase --continue
5、git commit --amend
6、git push -f XXX分支


## 参考链接
https://www.gss.com.tw/blog/%E4%BD%BF%E7%94%A8-git-rebase-interactive-%E6%A8%A1%E5%BC%8F%E6%95%B4%E7%90%86-commit

https://cloud.tencent.com/developer/article/1690638