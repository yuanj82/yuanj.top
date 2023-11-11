---
title: 自托管 Gitlab and 极狐 Gitlab 自动化 CI/CD 实践
tags: [Gitlab]
slug: 56d5b593
date: 2023-06-25 16:21:54
---

最近将代码全部迁到极狐的 Gitlab 了，极狐与原生的 Gitlab 几乎一模一样，不过对国内开发者的支持更好，Cli/CD 也不需要验证信用卡可以直接使用。但 Vercel 默认可以直接导入的仓库只有 GitHub、原生 Gitlab、Bitbucket 和 Azure，本来我是通过镜像仓库把仓库同步发到 GitHub 再用 Vercle 部署的，今天看到 Vercel 的文档说可以使用 Cli/CD 将项目部署到 Vercel，尝试了一下。

<!--more-->

## 自动部署博客到 Vercel

先到 Vercel 的 [token 设置](https://vercel.com/account/tokens) 中创建一个 token，加在 Gitlab 仓库 Cli/CD 的环境变量里面，变量名为`VERCEL_TOKEN`

然后在本地仓库创建`.gitlab-ci.yml`配置文件

内容如下

```yml
default:
  image: node:16.16.0

deploy_preview:
  stage: deploy
  except:
    - main
  script:
    - npm install --global vercel
    - vercel pull --yes --environment=preview --token=$VERCEL_TOKEN
    - vercel build --token=$VERCEL_TOKEN
    - vercel deploy --prebuilt  --token=$VERCEL_TOKEN

deploy_production:
  stage: deploy
  only:
    - main
  script:
    - npm install --global vercel
    - vercel pull --yes --environment=production --token=$VERCEL_TOKEN
    - vercel build --prod --token=$VERCEL_TOKEN
    - vercel deploy --prebuilt --prod --token=$VERCEL_TOKEN
```

直接 push 到远程仓库，项目名称会由 Vercel 自动创建，默认与仓库名是一致的

部署过程比较慢，大概三四分钟就可以在 Vercel 的控制台看到项目了

## 自动发布 npm 包

先在本地登录、初始化、发布包

然后再到 npm 官网创建一个 token，添加到 Cli/CD 的环境变量中

本地创建`.gitlab-ci.yml`配置文件

内容如下

```yml
image: node:latest

stages:
  - deploy

deploy:
  stage: deploy
  script:
    - echo "//registry.npmjs.org/:_authToken=\${NPM_TOKEN}">.npmrc
    - npm install
    - npm publish
  environment: production
```

`NPM_TOKEN`要与环境变量里的变量名一致

然后依次执行下列命令发布包

```bash
git add .
git commit -m "npm publish"  ## 提交信息，可自定义
npm version patch  ## 更新版本号
git push
```

后面每次发布新版本的包也是上面的命令

## 使用 Cloudflare Workers 部署站点

需要先克隆 [仓库](https://github.com/cloudflare/worker-sites-template/tree/wrangler2) 中的文件，并且修改 wrangler.toml 中的信息

```yml
default:
  image: ubuntu:latest

CF_Workers_deploy:
  stage: deploy
  only:
    - main
  script:
    - sed -i 's@//.*archive.ubuntu.com@//mirrors.ustc.edu.cn@g' /etc/apt/sources.list
    - apt-get update && apt-get install -y hugo curl
    - curl -sL https://deb.nodesource.com/setup_18.x | bash -
    - apt-get update && apt-get install -y nodejs build-essential
    - hugo
    - npm config set registry https://registry.npmmirror.com/
    - npm install wrangler -g
    - npm install @cloudflare/kv-asset-handler
    - wrangler deploy
  variables:
    CLOUDFLARE_API_TOKEN: $CF_API_TOKEN
    CLOUDFLARE_ACCOUNT_ID: $CF_ACCOUNT_ID
```

需要绑定两个变量 CF_API_TOKEN 和 CF_ACCOUNT_ID

## 参考文档

[发布 npm 包 | Gitlab](https://docs.gitlab.cn/jh/user/packages/npm_registry/#%E5%8F%91%E5%B8%83%E4%B8%80%E4%B8%AA-npm-%E5%8C%85)

[How can I use GitLab Pipelines with Vercel?](https://vercel.com/guides/how-can-i-use-gitlab-pipelines-with-vercel)