---
layout: post
title:  "使用Magit修改历史提交"
date:   2019-01-10 15:29:27 +0800
categories: programming
---

年中的时候搞了个比特币套利的bot，后来工作比较忙，没时间维护，想着放到 `GitHub`
上开源了吧，也许会有帮助。

结果忘记了个大事情，测试代码里有我的 key pair。大意了，整个repo删掉了。但是问题
来了，怎么才能修改一个历史上的 commit 呢。

# 修改当前Commit
熟悉 `Git` 的同学应该对这个都会比较熟悉，直接用 `git commit --amend` 就搞定了，
新的提交会被加到最近（也就是 `HEAD`）里。如果用 `Magit` 的话，好像是更简单了，只
需要 `c a` 就可以。

# 修改历史 Commit
而对我来说事情并非如此。

```
7e57bf60 * origin/coinex fcoindiff origin/fcoindiff change some exchange structure more general
df7857ef * implement coinex api
7941ccc4 * v0.01 master origin/master orderbook basic works
efebf494 * main function
25cd968e * time window
250a4e0c * simple order book manager
e79f35fa * implement fcoin api
4c3917c8 * first commit
```
需要修改的 commit 为倒数第二个，`Magit` 中也没有提供一个直接的命令来做这个操作。
所以这个操作需要多个步骤：
1. 将这个历史的 `commit` 放置到 `HEAD` 
2. 修改 `HEAD`
3. 将 `HEAD` 放回。

这个时候就需要 `rebase` 帮忙了。在 `Magit` 中， `r m` 启动 `edit a commit` 之后
`C-c C-c` 选中需要改的 commit, 之后 `git` 就会完成 `rewind` 操作将历史中的这个
commit 放在head 上，此时 `status` 中给出:

```
Rebasing master onto test~5
pick 7941ccc4 orderbook basic works
pick efebf494 main function
pick 25cd968e time window
pick 250a4e0c simple order book manager
stop e79f35fa implement fcoin api
onto 4c3917c8 first commit
```
之后更改需要更改的文件，`c a` 提交打现在的 `HEAD` 实际上也就是 `e79f35fa` 中，然
后 `r r` 执行 `rebase --continue` 如果没有冲突的话就搞定了。 

# 另外一种修复方式
上面说的这种操作稍微复杂一些，但是好处在于可以彻底删除 `git` 中留存的信息。如果
只是需要对某个历史修复做修改而非要彻底删除，可以使用 `git` 中的 `git commit
--fixup A` 操作，对应到 `Magit` 中为 `c f`。此命令会将改动生成一个新的 `commit`
添加到代码树中。

# Magit Rocks
Magit 真是个好东西
