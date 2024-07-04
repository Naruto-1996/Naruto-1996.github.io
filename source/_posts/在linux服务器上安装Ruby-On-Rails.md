---
title: 在linux服务器上安装Ruby_On_Rails
date: 2021-02-02 18:21:58
categories:
- rails
tags:
- ruby
- ubuntu
---

#### 在linux服务器上安装Ruby On Rails

##### 1、安装rbenv

先安装git

```
# mac
brew install git
# centos
yum install git
# ubuntu
apt-get install git
```

然后安装rbenv

```
# 安装rbenv到~/.rbenv目录
git clone git://github.com/sstephenson/rbenv.git ~/.rbenv
```

下面安装一下rbenv的插件

```
# 用来编译安装 ruby
git clone git://github.com/sstephenson/ruby-build.git ~/.rbenv/plugins/ruby-build

# 用来管理 gemset, 可选, 因为有 bundler 也没什么必要
git clone git://github.com/jamis/rbenv-gemset.git  ~/.rbenv/plugins/rbenv-gemset

# 通过 gem 命令安装完 gem 后无需手动输入 rbenv rehash 命令, 推荐
git clone git://github.com/sstephenson/rbenv-gem-rehash.git ~/.rbenv/plugins/rbenv-gem-rehash

# 通过 rbenv update 命令来更新 rbenv 以及所有插件, 推荐
git clone git://github.com/rkh/rbenv-update.git ~/.rbenv/plugins/rbenv-update

# 使用 Ruby China 的镜像安装 Ruby, 国内用户推荐
git clone git://github.com/AndorChen/rbenv-china-mirror.git ~/.rbenv/plugins/rbenv-china-mirror
```

然后需要将下面两句代码放在bash的配置文件中：

```
export PATH="$HOME/.rbenv/bin:$PATH"
eval "$(rbenv init -)"
```

linux是一般是放在~/.bashrc中，mac是放在~/.bash_profile中

修改完成后，执行下面的命令使其生效

```
# linux
source ~/.bashrc
# mac
source ~/.bash_profile
```

#### 2、安装ruby

rbenv install --list  # 列出所有 ruby 版本

例如安装2.7.2

```
rbenv install 2.7.2
```

设置使用的ruby版本, 有以下三种设置方式

```
rbenv global 2.3.3      # 默认使用2.3.3
rbenv shell 2.3.3       # 当前的 shell 使用2.3.3, 会设置一个 `RBENV_VERSION` 环境变量
rbenv local 2.3.3      # 当前目录使用2.3.3, 会生成一个 `.rbenv-version` 文件
```

#### 3. 安装rails

设置ruby版本后，安装rails：

```
# 在当前的ruby版本中安装rails
gem install rails
```

好了，这样就完成了rails的安装，rails已经可以使用了，但是为了更好的使用，请继续看后面的教程。

### 配置RubyGems镜像

gem是ruby管理依赖包的工具，而RubyGems的默认地址因为万恶的墙的关系很难访问到，因此需要配置RubyGems 镜像。这里使用的是[Ruby China](https://gems.ruby-china.com/)的镜像地址

命令行输入

```
gem sources --add https://gems.ruby-china.com/ --remove https://rubygems.org/
```

可以通过下面这个命令查看设置的结果是不是https://gems.ruby-china.com：

```
gem sources -l
```

### 修改bundle的源地址

bundler是rails管理gem依赖的工具，同样的，也需要修改其地址为ruby china的镜像

命令行输入

```
bundle config mirror.https://rubygems.org https://gems.ruby-china.com
```