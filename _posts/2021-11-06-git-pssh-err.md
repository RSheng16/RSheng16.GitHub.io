---
layout: post
title: "git push 错误"
description: "The brain is a good thing, but unfortunately I need to record"
tag: error
---

在GitHub中重新构建仓库后，git push 报错：

```
ERROR: Permission to RSheng16/Rsheng16.github.io.git denied to deploy key
fatal: Could not read from remote repository.
```

添加SSH-key时，出现问题，说

```
Key is already in use
```
显然这个密钥已经不能重复使用了，那么对于A项目使用了该密钥，那么B项目要正常git push该如何做了？

## 解决办法

在用户 Setting ——>> SSH and GPG keys ——>> New SSH key，复制粘贴现有的ssh-key，此操作相当于配置了一个全局的SSH-key。

这样就可以在此电脑上操作GitHub上不同的仓库了，是有点酷。

## git push

![](/images/git/git-push.png)


