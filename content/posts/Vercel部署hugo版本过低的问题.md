---
title: Vercel 部署 hugo 版本过低的问题
tags:
  - "Vercel"
slug: j5n0e5s0
date: 2023-08-19T08:16:21+08:00
---

今天使用 Vercel 部署 hugo 博客，结果报错。

<!--more-->

报错信息：

```
Error: add site dependencies: load resources: loading templates: "/vercel/workpath0/themes/meme/layouts/partials/third-party/lunr-search.html:8:1": parse failed: template: partials/third-party/lunr-search.html:8: function "warnf" not defined
```

查了一下，是 Vercel 默认使用的 hugo 版本太低了，需要指定版本，最好与建站时的版本一致，否则也可能会出问题。

仓库根目录新建文件`vercel.json`：

```json
{
    "build": {
      "env": {
        "HUGO_VERSION": "0.119.0"
      }
    }
  }
```