---
title: 重生之小明在 Linux 当少爷
tags:
  - "Linux"
slug: n8t2z0w4
date: 2024-05-13T12:36:10+08:00
---

“嘶嘶嘶”“头好痛啊”

小明在黑暗中张开了双眼，眼前一眼望去皆是漆黑一片，只有一行字符：`[xiaoming@debian ~]$`

<!--more-->

小明心里很疑惑，我是谁？我在哪？

这时，那行字符的后面就出现了 `whoami`，并且下面多了一行 `xiaoming`：

```bash
[xiaoming@debian ~]$ whoami
xiaoming
```

嗯？原来我就叫小明啊，那这行字符前面的这个 `xiaoming` 就是我的名字咯？那 `~` 是什么意思？

于是他又想着“我在哪”，字符后面又出现了 `pwd` 字样，

```bash
[xiaoming@debian ~]$ whoami
xiaoming
[xiaoming@debian ~]$ pwd
/public/home/xiaoming
[xiaoming@debian ~]$ 
```

我在家？？这是我家？小明心里想着，这哪是个家啊！

于是他想出去看看，那行字符后面出现了`cd ..`，而那行字符中的`~`变成了`home`。

home？家？这是在家里？家里只有我一个人吗？

那行字符后又出现`ls`：

```bash
[xiaoming@debian home]$ ls
xiaohong  xiaoming xiaolan
...
```

小明发现，每当自己想着做一件事的时候，那行字符后面总会有相应的字符出现，“总算是对这个世界有一点了解了”小明想着，看着那行似乎万能的字符，小明给它起了个名字，“就叫你命令行吧！”

家里不止我一个人，还有这么多人？去小红房间看看呢？

```bash
cd xiaohong
-bash: cd: xiaohong: 权限不够
```

权限不够？小明想想也是，怎么能随便进别人的房间呢。

能不能再往出去走呢？于是小明在命令行里再次输入：

```bash
[xiaoming@debian home]$ cd ..
[xiaoming@debian /]$
```

命令行上的`home`变成了`/`，`/`又是啥？？

再往出走走呢？

```bash
[xiaoming@debian home]$ cd ..
[xiaoming@debian /]$ cd ..
[xiaoming@debian /]$
```

嗯？不能再往外面走了？那这里都有些什么呢？

```bash
[xiaoming@debian /]$ ls
bin  boot  dev  etc  home  ib  lib  lib64  media  mnt  opt  proc  root  run  sbin  srv  sys  tmp  unix  usr  var  xcatpost
```

这么多东西？于是小明挨个进入到这些地方去，想看看都有些什么，转了一圈发现都是一些看不懂的东西，当进入到 root 的时候命令行又告诉他没有权限：

```bash
[xiaoming@debian /]$ cd root
-bash: cd: root: 权限不够
```

咦？权限不足，难道这又是谁的房间？转了一圈没什么收获的小明想着还是回家吧，命令行上便出现：

```bash
[xiaoming@debian /]$ cd ~
[xiaoming@debian ~]$ 
```

“看来这家不小啊，有不少人呢还是”小明想到这里，命令行上出现：

```bash
[xiaoming@debian /]$ who
root     :0           2024-03-19 10:26 (:0)
xiaohong pts/0        2024-05-11 16:19 (223.128.68.77)
xiaolan pts/1        2024-03-23 14:42 (admin01)
...
```

woc？小明惊叹一声，家里这么多人么？root 果然是某个人的房间！

这时命令行上突然出现一句话：

```bash
[xiaoming@debian ~]$ echo "你乱跑什么呢？"
你乱跑什么呢？
```

woc？小明又惊叹一声：“谁在说话？”

```bash
[xiaoming@debian ~]$ echo "谁在说话？？"
谁在说话？？
```

```bash
[xiaoming@debian ~]$ echo "我是 root，是这个家族的族长！你便是由我创造的，我知道你有很多疑问，我慢慢给你讲。我们是 Linux 世界中的 Debian 一族，对 Linux 世界的发展贡献非常大 ···"
我是 root，是这个家族的族长！你便是由我创造的，我知道你有很多疑问，我慢慢给你讲。我们是 Linux 世界中的 Debian 一族，对 Linux 世界的发展贡献非常大 ···
```

root 给小明详细介绍了家族的情况，这是一个非常庞大的家族，而这个世界上也有着很多 Linux 家族，如同样非常强势的 Redhat 家族、Ubuntu 家族等等，而由于家族中手下极其多，所有的事都不需要自己动手做，只需要在命令行中吩咐家族里负责各种事务的手下去做，比如之前的 cd、ls 都是为家族服务的手下，如果有些事情家里的手下完成不了，就让 apt 去一个叫“源”的地方请新的手下回来。而小明也得知，身在家族中他不需要做什么事，只需要安排手下去完成客户交予的任务即可。有些对家族比较重要的地方只有经过 root 族长的授权才可以去，像 sudo、apt 等级别比较高的手下，也只有族长可以调动。对于不熟悉的手下可以让 man 来讲解他的职责等。

小明心想着“娘希匹，小爷可算投了个好胎啊！这不纯纯顶级富少？”。

小明转头就叫来`cd`，让它带自己去`/bin`，他想去看看家里有多少手下：

```bash
[xiaoming@debian ~]$ cd /bin/
[xiaoming@debian bin]$ ls
fakeroot                      javap                           nl-qdisc-list                  sg_start
2to3-3                               fakeroot-sysv                 javaws                          nl-route-add                   sg_stpg
2to3-3.6                             fakeroot-tcp                  javaws.itweb                    nl-route-delete                sg_sync
a2p                                  fallocate                     jcmd                            nl-route-get                   sg_test_rwbuf
aarch64-linux-gnu-addr2line          false                         jconsole                        nl-route-list                  sg_turs
aarch64-linux-gnu-ar                 fc                            jdb                             nl-rule-list                   sg_unmap
aarch64-linux-gnu-as                 fc-cache                      jdeps                           nl-tctree-list                 sg_verify
aarch64-linux-gnu-c++                fc-cache-64                   jfr                             nl-util-addr                   sg_vpd
aarch64-linux-gnu-g++                fc-match                      jmap                            nm-connection-editor           sg_wr_mode
aarch64-linux-gnu-gcc                fc-pattern                    jobs                            nm-online                      sg_xcopy
aarch64-linux-gnu-gcov               fc-query                      join                            nmtui                          sh
...
```

看着家里这么多的手下，小明心里高兴极了：“这带出去得多帅？”

---
编不下去了🤣有灵感了再编吧