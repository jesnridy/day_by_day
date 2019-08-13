# git常用的命令

## git revert和git reset的区别
1. git revert是用一次新的commit来回滚之前的commit。
2. git reset是直接删掉指定的commit。
3. 在后面的再去合并老的分支时候，使用revert的操作会导致这部分改变不会再次出现（你如我dev分支revert了一个test1分支，然后后面我又需要test1的代码了，直接把test1的分支merge到dev分支会提示已经更新到最新了，解决办法就是再去执行个revert把之前的revert的代码再revert回来）。

## 应用场景
1. git revert是回退的代码后面还需要就用revert，如果是分支提交错了，不想让人发现，那就reset。
2. 