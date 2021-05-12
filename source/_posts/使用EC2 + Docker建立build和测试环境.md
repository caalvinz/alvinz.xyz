---
date: 2020-12-03 00:41:44
title: 使用EC2 + Docker建立build和测试环境
tags: AWS, EC2, Docker, 测试环境
---

# 使用EC2 + Docker建立build和测试环境

## 目标

使用docker建立一个适用多种应用程序需要的测试环境

1. 支持多个字域名
2. 提供免费的SSL支持（对app、小程序都是必须的）
3. 一个MySQL数据库
4. 自动编译
5. 自动部署



## 配套

- AWS EC2

  - Proxy：Nginx
  - SSL：Let's Encrypt
  - Docker + Docker Compose

- AWS RDS MySQL 

  

## 使用Nginx建立Proxy



# ![image-20201203091044504](./使用EC2%20+%20Docker建立build和测试环境/image-20201203091044504.png)



```
# Ensure that your version of snapd is up to date
sudo snap install core; sudo snap refresh core

# Install Certbot
sudo snap install --classic certbot

# Prepare the Certbot command
sudo ln -s /snap/bin/certbot /usr/bin/certbot

# Choose how you'd like to run Certbot
# Either get and install your certificates...
sudo certbot --nginx
```

Test automatic renewal

```
sudo certbot renew --dry-run
```

The command to renew certbot is installed in one of the following locations:

```
/etc/crontab/
/etc/cron.*/*
systemctl list-timers
```



# 运行环境（build环境）

基于php:7.4-fpm搭建，然后进入docker实例中，安装必要的环境，然后build docker image用于部署。

```bash
# Install
apt-get update && apt-get install -y \
    git \
    curl \
    libpng-dev \
    libonig-dev \
    libxml2-dev \
    zip \
    vim \
    unzip

# Clear cache
apt-get clean && rm -rf /var/lib/apt/lists/*

# Install PHP extensions
docker-php-ext-install pdo_mysql mbstring exif pcntl bcmath gd

# Installing PHP Composer on Ubuntu
sudo apt update
sudo apt install wget php-cli php-zip unzip
wget -O composer-setup.php https://getcomposer.org/installer
php composer-setup.php --version=1.10.10 --install-dir=/usr/local/bin --filename=composer
composer --version
```



# Laravel

下面的要素的关系

1. 源代码（所以依赖还没有安装）
2. .env环境
3. docker image

源代码要放到docker image中吗？

