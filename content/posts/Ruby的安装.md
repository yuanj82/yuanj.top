---
title: Ruby 的安装
tags:
  - "Ruby"
  - "Linux"
slug: j6d3g7l9
date: 2023-09-05T18:51:46+08:00
---

不得不说，Ruby 的安装实在坑太多，Debian 上安装了老半天才成功，记录一下安装过程，安装的是 3.2.2 版本。

<!--more-->

```bash
#!bin/bash

# Download Ruby binary package
wget https://cache.ruby-china.com/pub/ruby/3.2/ruby-3.2.2.tar.gz

# Decompress Ruby binary package
tar -vxf ruby-3.2.2.tar.gz
mv ruby-3.2.2 ruby
mv ruby ~/

cd ~/ruby

# Install the required dependencies for Ruby first
sudo apt-get install libyaml-dev libssl-dev libedit-dev libffi-dev 

# Compile and installation
./configure
make
sudo make install

# Bundler and gem swapping sources
gem sources --add https://mirrors.tuna.tsinghua.edu.cn/rubygems/ --remove https://rubygems.org/
gem install bundler
bundle config mirror.https://rubygems.org https://mirrors.tuna.tsinghua.edu.cn/rubygems
```