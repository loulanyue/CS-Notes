# Git for Windows 国内下载站
### Git for Windows. 国内直接从官网（http://git-scm.com/download/win）
下载比较困难，需要翻墙。这里提供一个国内的下载站

找不到想要的版本？可以访问 淘宝 NPM 的 Git for Windows 索引页 https://npm.taobao.org/mirrors/git-for-windows/ 以下载更多版本。

The latest (v2.21.0) version of Git for Windows, was released on 2019-2-26.

# v2.21.0 (2019-02-26)

64-bit Git for Windows Setup : https://npm.taobao.org/mirrors/git-for-windows/v2.21.0.windows.1/Git-2.21.0-64-bit.exe

64-bit Git for Windows Portable : https://npm.taobao.org/mirrors/git-for-windows/v2.21.0.windows.1/PortableGit-2.21.0-64-bit.7z.exe

# 一、Git是一款免费、开源的分布式版本管理系统

安装
wget https://github.com/git/git/archive/v2.8.0.tar.gz

依赖：
yum -y install zlib-devel openssl-devel cpio expat-devel gettext-devel curl-devel perl-ExtUtils-CBuilder perl-ExtUtils-MakeMaker

windows
1.官网下载
https://git-for-windows.github.io/

git基础配置

        1、配置用户名
        git config --global user.name "loulanyue"
        2.配置邮箱
        git config --global user.email "260355617@qq.com"
        3.其他配置
        git config --global merge.tool "kdiff3"
        没装KDiff3就不用设
        git config --global core.autocrlf false
        让Git不要管Windows/Unix换行符转换的事

2、编码配置

        git config --global gui.encoding utf-8

        git config --global core.quotepath off

        windows上还需要配置：
        git config --global core.ignorecase false


git ssh key pair 配置

        1.在linux的命令行下，或Windows上Git Bash命令行窗口中键入：
        ssh-keygen -t rsa -C "260355617@qq.com"
        回车
        ssh-add ~/.ssh/id_rsa
        cat ~/.ssh/id_rsa.pub
        
        刚开始使用git，添加私钥时发生如下错误

        ssh-add ~/.ssh/id_rsa
        
        输出错误： Could not open a connection to your authentication agent

        解决此问题的方法是执行下

        eval `ssh-agent -s`
        然后再次执行ssh-add ~/.ssh/id_rsa就可以顺利执行了
        
        
        另外：若执行ssh-add /path/to/xxx.pem是出现这个错误:Could not open a connection to your authentication agent，则先执行如下命令即可：

　　     ssh-agent bash

git验证

git --version

git常用命令

1.切换分支

git checkout 分支名

2.拉取

git pull

3.提交

git push



ll

make prefix=/usr/local all

make prefix=/usr/local install

git version

yum install git

n

mkdir gitdownload

git clone git@gitee.com:loulany/mmall.git



IDEA在Windows下提交：

1.连接：gitee配置SSH

    cd ~/.ssh
    ssh-keygen -t rsa -C "260355617@qq.com"
    eval `ssh-agent -s`
    ssh-add ~/.ssh/id_rsa
    cat ~/.ssh/id_rsa.pub
    
测试：

    ssh -T git@gitee.com


2.提交

    git init

    git add .

    git status

    git commit -am "first commit init project"

    git remote add origin git@gitee.com:loulany/mmall.git

    git pull

    git push -u -f origin master

    git branch -r

    git checkout -b v1.0 origin/master

    git branch

    git push origin HEAD -u
    
    $ git push -u origin master


# error: failed to push some refs to 'git@github.com:loulanyue/javabasic.git'

        To github.com:loulanyue/javabasic.git
        ! [rejected]        master -> master (fetch first)
        error: failed to push some refs to 'git@github.com:loulanyue/javabasic.git'
# 解法：
# git pull origin master
# git push -u origin master 


