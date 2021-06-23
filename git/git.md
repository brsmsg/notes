### merge & rebase

git 工作流：很重要的两张图！！

![img](https://user-gold-cdn.xitu.io/2018/5/9/16343316cbad1533?imageView2/0/w/1280/h/960/format/webp/ignore-error/1)



![img](https://user-gold-cdn.xitu.io/2018/5/9/1634331848b2fff5?imageView2/0/w/1280/h/960/format/webp/ignore-error/1)

#### merge

![merge](https://user-gold-cdn.xitu.io/2018/5/9/16342fbc3161f98e?imageView2/0/w/1280/h/960/format/webp/ignore-error/1)

merge之后的分支记录

![使用 `git merge` 之后的分支提交记录](https://user-gold-cdn.xitu.io/2018/5/9/16342fc20a5fe006?imageView2/0/w/1280/h/960/format/webp/ignore-error/1)



需要保留详细合并信息时使用merge。merge会保存所有提交的信息：引出/合并分支全部保存，提交历史信息杂乱

#### rebase

![merge](https://user-gold-cdn.xitu.io/2018/5/9/16342fc20a6c6c8f?imageView2/0/w/1280/h/960/format/webp/ignore-error/1)

rebase之后的分支记录

![完成 `git rebase --continue`的 feature](https://user-gold-cdn.xitu.io/2018/5/9/16342fc54cf6e1a2?imageView2/0/w/1280/h/960/format/webp/ignore-error/1)

* rebase可以合并多次无意义提交，同时没有多余的合并记录，commit顺序不一定按照commit提交时间排列。
* 改变当前分支从master上拉出分支的位置。
* merge是在master分支上使用git merge feat， rebase需要在feat分支上进行git rebase master。当前分支在master上变基。要对每一次提交的内容进行冲突解决。 如上图例子，需要解决四次冲突
* 可以使用squash合并commit



## reset & revert

### reset

reset后跟的是要回退到的版本号

* -- soft：回退后之前修改的代码变为add状态，
* --hard：回退后重制索引和working tree，所有没提交的代码都被丢弃

push到远端需要push -force

### revert

revert后需要跟具体回退的版本号，也就应该是reset目标的后一个版本号。

commit 1 - commit 2 - commit 3

我们执行revert commit-2

commit 1 - commit 2 - commit 3 - commit 4

相当于只是多了一条commit，把该分支上的所有修改都改回去了。



## stash

当我们写到一半，工作流被打断。需要切换到别的分支改代码，就需要使用git stash。

git stash list查看stash列表

操作完后使用git stash pop