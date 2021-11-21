---
title: 创建django项目
toc: true
tags:
  - django
  - 术士之战
categories:
  - django项目
  - 术士之战
date: 2021-11-21 21:57:47
updated:
---
<!--more-->  

> 内容基于yxc工程课系列django工程课，源自yxc视频、讲义以及他人题解等，仅用作个人记录，查询

## Linux Django-2 配置docker、git环境与项目创建  
### 配置docker环境
1. scp /var/lib/acwing/docker/images/ django_lesson_1_0.tar server_name:  # 将镜像上传到自己租的云端服务器  
2. ssh server_name # 登录自己的云端服务器   
3. docker load -i django_lesson_1_0.tar # 将镜像加载到本地
4. docker run -p 20000:22 -p 8000:8000 --name django_server -itd django_lesson:1.0 # 创建并运行django_lesson:1.0镜像 (端口要自己去云平台放行)
5. docker attach django_server # 进入创建的docker容器  
6. passwd # 设置root密码  
7. adduser acs # 创建普通用户acs  
8. usermod -aG sudo acs # 给用户acs分配sudo权限  
9. su -acs# 可切换到用户acs中  
10. ctrl p + ctrl q #挂起容器  
11. 返回AC terminal，为acs用户配置别名和免密登录  
12. vim config # 配置别名
    - Host xxx # 配置别名
    - Host Name xxx.xxx.xxx.xxx # 自己的ip地址
    - User xxx # 自己的用户名
    - Port xxx # 登录的端口号，22可省略
13. ssh-copy-id 别名 # 为acs用户一键添加公钥，免密登录  
14. scp .bashrc .vimrc .tmux.conf 别名: # 配置一下环境（acwing传家宝三件套）    

---

### 配置git环境
1. tmux # 打开tmux
2. django-admin startproject acapp # 创建django项目acapp
3. ssh-keygen # 生成密钥用于连接到ac git上面
      - 在git偏好设置中，打开ssh密钥，添加一下刚才生成的公钥(.pub)
4. git init # 进到acapp中将其配置成git仓库  
5. 打开git，在git上创建一个仓库（项目）按照下面的提示在acs里面配置一下git
     - git config --global user.name xxx # 配置全局用户名
     - git config --global user.email xxx@xxx.com # 配置全局邮箱
     - git add . # 把所有更改信息加入
   - git commit -m "xxx" # 添加修改信息
   - git remote add origin git@git.acwing.com:xxx/XXX.git #建立连接
   - git push --set-upstream origin master # 把更新推送到git上

---

### 项目施工
1. 再打开一个tmux界面，（一个tmux用于维护控制台，另一个tmux用于开发项目）启动项目  
- python3 manage.py runserver 0.0.0.0:8000 # 0.0.0.0代表允许所有ip访问
2. ag ALLOWED-HOSTS # 全文搜索字段所在位置，并将自己的IP地址添加到列表里
3. git 一下代码
   - 注意：在git仓库的根目录下（acapp）,vim .gitignore在文件里面写上 **/__pycache__(两个杠)可以忽略这类不必要的文件 *.swap可以忽略临时文件
4. python3 manage.py startapp game # 创建gameapp

5. 登录django管理员界面
     - ctrl c 先关掉控制台
     - python3 manage.py migrate #同步一下数据库的修改
     - python3 manage.py createsuperuser # 创建管理员账号
          - 输入username以及密码等
     - pyhton3 manage.py runserver 0.0.0.0:8000 # 启动控制台
     - IP地址：8000/admin # 进到管理员登录界面，输入一下刚才创建的账号即可进到管理员界面
6. cd game
     - touch urls.py
     - mkdir templates
     - vim views # 定义一个index函数
     - vim urls.py # 写一下路由
     - cd acapp vim urls.py # 将刚才的路由写到总路由里面
     - models：存储各种数据结构（class等）
     - views：存储函数
     - urls：路由
     - templates：存储网页模板
7. git一下代码
    - git status # 查看修改状态，红色是修改的
    - git add . # 把所有修改加入带上传队列
    - git status # 再次查看修改状态，已加入为绿色，部分未加入的可手动加一遍
    - git commit -m "xxx" # 添加修改信息
    - git push # 把修改传到git仓库

----

----
**欢迎讨论指正**