# git操作

### git配置

> [https://blog.csdn.net/qq\_34898847/article/details/125687874](https://blog.csdn.net/qq_34898847/article/details/125687874 "https://blog.csdn.net/qq_34898847/article/details/125687874")

### git 命令操作

#### 基础命令

-   初始化git仓库: git  init xzb
-   克隆项目 git  clone xx.git
-   将本地仓库和远程仓库关联:  git remote add orgin  xxx.git远程仓库地址
-   git branch、git branch -r、git branch -a     git branch -d 分别是查看本地分支、查看远程分支、查看所有分支、删除分支（-D强制删除）
-   git clean -f   删除本地仓库中未跟踪的文件，如果想删除的是目录，使用 `-d`
-   切换分支 git checkout 分支名称
-   创建并切换到新分支： git checkout -b 新分支名称
-   查看关联的远程仓库列表 git remote -v
-   基于当前分支创建分支：  git branch dev  (基于当前分支创建一个dev分支，eg：  git  branch test dev (基于当前dev分支创建一个test分支))
-   基于远程分支创建分支：  git branch test orgin/master (基于远程的master分支创建一个test分支)
-   查看提交日志： git log    (git  log  --graph   git  log  --oneline   )
-   合并分支： git merge <其他分支名>  （(将其他分支里的提交内容合并到当前分支里)

### 拉取推送

-   origin 是远程仓库（主机）的名字，是 Git 的默认叫法
-   拉取合并项目: git pull <远程主机名> <远程分支名>:<本地分支名>   （*从远程仓库拉取代码并合并到本地，可简写为 git pull 等同于 git fetch && git merge* ）
-   git fetch <远程主机名> <分支名>     （*获取远程仓库特定分支的更新*： 仅仅只会拉取远程的更改，不会自动进行 merge 操作。对你当前的代码没有影响）
-   git fetch --all       （*获取远程仓库所有分支的更新*）
-   git push <远程主机名> <本地分支名>:<远程分支名>   （eg.  git  push origin  master)   git push origin <本地分支名>
-   git  push --set-upstream orgin master    (把本地分支提交到远程orgin的分支,并创建master分支)
-   git push --force  强制推送本地分支到远程仓库（这个操作可能会删除别人刚提交的代码，是不安全的）
-   git  push --force-with-lease  强制推送本地分支到远程仓库 （如果远程仓库有被新的提交，出现version不一致，则不允许覆盖，是安全的操作；在此之前先进行git  fetch ）解决的是本地仓库不够新时，依然覆盖了远端新仓库的问题

### 文件差异

-   git   diff  相关操作（查看具体文件的修改内容）
-   git   diff    不写文件路径则是查看所有差异
-   git    diff  文件路径    比较某文件的差异变化 （通过q命令退出）

### 文件修改状态

-   git status 相关操作（掌握仓库当前的状态，查看当前本地分支的修改状态）
-   git status --ignored  列出准备提交和被忽略的文件（默认不会列出被忽略的文件）
-   git  status --short    （一种简洁的方式列出被修改的文件git status --s  ）
-   即撤回工作区的修改： 文件在工作区被修改，还未提交到缓存区就可以使用 `git checkout -- <filename>` 的命令，如果要撤回多个文件的修改用空格分开
-   文件从工作区到达了缓存区了，可以用： `git reset <filename>` 的命令来对特定的文件进行撤销
-   git  rm -cached 文件    删除暂存区文件

### 文件添加暂存区

-   git   add 相关操作（添加文件内容到暂存区）
-   git add   .     使用它会把工作时的所有变化提交到暂存区，包括文件内容修改(modified)以及新文件(new)，但不包括被删除的文件
-   git  add -u    提交被修改(modified)和被删除(deleted)文件，不包括新文件(new)
-   git  add  -A   是上面两个功能的合集（git add –all的缩写）  (new/update/delete提交所有变化)
-   git  add   file1  file2    多个文件用空格分隔
-   git  add   文件夹名
-   git commit -m '添加所有,提交所有'  （git  commit -am '直接提交到本地分支'）
-   git commit --amend      *对最近一次的提交的信息进行修改,此操作会修改commit的hash值*
-   git  rm -cached 文件    删除暂存区文件:  （git rm abc \*.cpp，可使用正则）我们想保留该文件,但又想把删除的改动添加到暂存区git  rm -cached 文件    删除暂存区文件:  （git rm abc \*.cpp，可使用正则）我们想保留该文件,但又想把删除的改动添加到暂存区
-   `git rm 文件`  只是简单的在工作目录里手动删除文件,  则帮我们在删除的同时，也将修改添加到了暂存区

### 文件回滚到某一个版本

-   git   reset   相关操作（git reset --soft; git reset --mixed; git reset --hard;）[https://www.jianshu.com/p/cbd5cd504f14](https://www.jianshu.com/p/cbd5cd504f14 "https://www.jianshu.com/p/cbd5cd504f14")
-   git   reset  --soft   回滚到上一个版本的版本号      将本地仓回滚到上一个版本号，此时暂存区和工作区的内容还是不会回滚（可通过git rm --cached 删除缓存区数据，然后变工工作区代码，从新进行提交）
-   git reset --mixed  或者  git reset     （默认方式，本地仓和暂存区，都回滚到上个版本号。工作区代码不受影响）
-   git  reset  --hard   版本号      （本地仓、暂存区、工作区，三区都回滚；特别注意本地代码会被回滚到指定版本号）
-   git rese --hard  HEAD^    (回退到上一个版本，一个^  代表一个版本，2个用HEAD^^  )
-   `git revert` 撤销某次操作，此操作不会修改原本的提交记录，而是会新增一条提交记录来抵消某次操作。
-   `git revert <commit-id>` 针对普通 commit
-   `git revert <commit-id> -m` 针对 merge 的 commit
-   个人的 feature 分支而言，可以使用 `git reset` 来回退历史记录，之后使用 `git push --force` 进行推送到远程，但是如果是在多人协作的集成分支上，不推荐直接使用 `git reset` 命令，而是使用更加安全的 `git revert` 命令进行撤回提交。这样，提交的历史记录不会被抹去，可以安全的进行撤回

### 暂存区

-   我们开发到一半，需要重新创建一个新分支去解决线上问题，但此时又不想将当前的分支改动提交到对应分支上，则可以使用 git stash 将修改（包括工作区和暂存区）保存到堆栈中等新分支处理完毕后，就可以切换到之前的分支，然后使用 `git stash pop` 恢复缓存的堆栈内容了。
-   git stash //把本地的改动暂存起来
-   git stash save "message" 执行存储时，添加备注，方便查找。
-   git stash pop // 应用最近一次暂存的修改，并删除暂存的记录
-   git stash apply  // 应用某个存储,但不会把存储从存储列表中删除，默认使用第一个存储,即 stash@{0}，如果要使用其他个，git stash apply stash@{\$num} 。
-   git stash list // 查看 stash 有哪些存储
-   git stash clear // 删除所有缓存的 stash
-   推荐在使用 stash 的相关命令时，每一次暂存的时候，不要直接使用  `git stash`  命令进行暂存下来，而是使用  `git stash save "message..."`  这种方式，给本次的提交做一个信息的记录。这样，想应用更改的时候，先通过 `git stash list` 查看一下所有的暂存列表。之后，推荐使用 `git stash apply stash@${num}` 的方式进行应用对应的 stash，这样不会清空已有的 stash 的列表项，并且能应用到当前的工作区，不需要这个暂存的话，再手动清除就可以了

### 基变rebase

-   git  rebase 让你的提交记录更加清晰可读；他的作用和 merge 很相似，用于把一个分支的修改合并到当前分支上
-   git rebase origin/master  (以远程master分支作为基础点， 然后把当前的分支合并到基础点之后； 这样git  log查看分支线就没有分支线了；我们的提交记录就会非常清晰，没有分叉)
-   大部分情况下，rebase 的过程中会产生冲突的，此时，就需要手动解决冲突，然后使用依次 `git add ` 、`git rebase --continue ` 的方式来处理冲突，完成 rebase 的过程，如果不想要某次 rebase 的结果，那么需要使用 `git rebase --skip ` 来跳过这次 rebase 操作
-   git  rebase --skip    放弃rebase结果
-   git  rebase --abort  放弃解决冲突结果
-   git  merge 与get rebase区别：git merge后会产生一条额外的合并记录，类似 `Merge branch 'xxx' into 'xxx'` 的一条提交信息；而用git rebase 的时候 ，需要依次解决每次的冲突，才可以提交。
-   git rebase -i \<base-commit>  ：git rebase 交互模式， 在开发中，常会遇到在一个分支上产生了很多的无效的提交，这种情况下使用 rebase 的交互式模式可以把已经发生的多次提交压缩成一次提交，得到了一个干净的提交历史；参数 `base-commit` 就是指明操作的基点提交对象（版本号），基于这个基点进行 rebase 的操作，对于已经提交的历史记录，通过基准点把之前的提交压缩成一次提交： 比如git rebase -i  ac18082  会进入一个vim页面进行保留一个pick操作；按下`:` 然后 `wq` 保存退出，此时又会弹出一个编辑页面，这个页面是用来编辑提交的信息，修改为 `feat: 更正`，最后保存一下；rebase 操作可以让我们的提交历史变得更加清晰
-   git cherry-pick     获取指定的 commit；它会获取某一个分支的单笔提交，并作为一个新的提交引入到你当前分支上。当我们需要在本地合入其他分支的提交时，如果我们不想对整个分支进行合并，而是只想将某一次提交合入到本地当前分支上，那么就要使用 `git cherry-pick` 了
-   git cherry-pick \[commit-hash]   commit-hash 表示的是某次 commit 的 hash 值。比如，现在，依次执行以下两条指令 `git cherry-pick e0ssss3`、`git cherry-pick c9asss01`，过程中，如果出现冲突，解决冲突后 进行 `git add `，接着执行 `git cherry-pick --continue`  ；最后则会分别把这2个分支的修改都追加到当前分支的后面

### 命令简化

> git config --global alias.<简化的字符> 原始命令    (比如：git config --global alias.co checkout)

### 解决冲突

-   解决冲突:  git  pull  拉取分支后,  出现冲突, 查看文件, head 部分是代表自己的文件,   -> 有编号的的是远程仓库的版本
-   添加多个远程仓库到本地
    [https://blog.csdn.net/WeiHao0240/article/details/103390698](https://blog.csdn.net/WeiHao0240/article/details/103390698 "https://blog.csdn.net/WeiHao0240/article/details/103390698")
    git merge --abort    合并是有可能有冲突的，如果冲突后想放弃合并，可以使用这个命令
    -   git  remote  add orgin   xx1.git   (orgin1)
    -   git remote  add orgin  xx2.git    (orgin2)
        git push  --set-upstream orgin2  master  (把当前的master分支提交到orgin2中)

### 文件提交流程

> 查看仓库里修改了哪些文件  git status -s
> 把需要保存的文件修改添加到暂存区  git add 文件或目录
> 查看有哪些文件已经添加到暂存区  git status -s
> 把修改提交到仓库  git commit  -m
> 查看日志检查提交信息是否正确  git log -3
> 远程分支同步远程仓库最新内容   git fetch origin
> 如果出现冲突：

```纯文本
        1.   git merge --abort    合并是有可能有冲突的，如果冲突后想放弃合并，可以使用这个命令
        2.   git log --oneline --graph  查看日志轨迹图
        3.   git  branch  20220720  创建一个临时分支，避免解决冲突的时候代码丢失
        4.   git reset --mixed  HEAD^   指定回滚到上个分支，或者某一个分支，进行相关命令操作 或者使用--heard(注意会删除本地已经提交的代码)
        5.   git fetch origin   再次抓取远程分支，； 解决冲突
        6.   git rebase origin/master  将本地分支代码推送到远程分支代码
        7.   与本地分支进行合并： git cherry-pick  分支号xx     对于多分支的代码库，将代码从一个分支转移到另一个分支是常见需求
                  1.   一种情况是，你需要另一个分支的所有代码变动，那么就采用合并（`git merge`）
                  2.   另一种情况是，你只需要部分代码变动（某几个提交），这时可以采用 Cherry pick
        8.   解决冲突，然后git stauts
        9.   冲突解决完成后，继续rebase操作： git  rebase  --continue  或者  git cherry-pick --continue --no-edit
        10.   提交： git commit --allow-empty
        11.   推送远程： git push origin 本地分支：远程分支
```

> 把本地提交的修改重放到远程分支最后   git rebase origin/master

> 查看仓库有多少个提交将推送到远程仓库   git status  -s

> 推送本地提交到远程仓库的引用空间进行审核    git push origin  本地分支：远程分支

### idea中常用命令

> merge  into current : 把选择的分支合并到当前分支
> 合并代码后 , 需要 git push 到远程分支
> [https://www.cnblogs.com/jay-wu/p/10518231.html](https://www.cnblogs.com/jay-wu/p/10518231.html "https://www.cnblogs.com/jay-wu/p/10518231.html")
> Checkout : 检出这个分支。
> Checkout As... ： 使用这个分支创建新的分支并检出到新分支。
> Compare With... : 使用当前分支与所选分支做代码提交比较。
> Rebase Current onto Selected: 在当前分支做变基。（将所选分支提交加入到当前分支）
> Checkout with Rebase : 检出所选分支并做变基。（将当前分支提交加入到所选分支）
> Merge into  Current： 合并到当前分支（将所选分支合并到当前分支）
> Rname：修改所选分支名称
> Delete ：删除所选分支

### master分支合并到master分支

> 流程如下：
> 一、将分支切换到master
> git checkout master
> 二、将代码pull到本地
> git pull
> 三、修改冲突
> vs工具或者idea工具等
> 四、提交到本地
> git add .
> git commit -m "merge"
> 保证 master在本地分支的代码是最新的, 所以需要先切换到master分支,然后切换到dev分支, 使用merge把master分支合并到dev分支, 再进行git push origin dev
> 五、切换到你所在分支dev
> git checkout dev
> 六、merge
> git merge master
> 七、将本地内容push到dev分支
> git push origin dev

## 参考链接

<https://wiki.7wate.com/getting-started/Git/入门/分支>
