---
title: Linux的用户和权限管理
---

## 用户管理

> *相关文件*
> *1. /etc/passwd*
> *2. /etc/shadow*

### /etc/passwd格式
name:password:UID:GID:GECOS:directory:shell
*注:GECOS为注释*
### /etc/shadow格式
name:encrypted password:last modified:最短使用期限：最长使用期限：警告时间：过期时间：保留

    useradd
        -u,
        -g,
        -G, 指明用户所属的附件组
        -c，注释
        -d, 家目录
        -s，shell
        -r，系统用户

    usermod
        同useradd
        -a, 追加新的附加组
        -m，移动家目录
        -l，修改用户名

    userdel
        -r, 把家目录一并删除

    passwd: 修改密码

    id
        -u
        -g: 基本组
        -G: 所属的所有组
        -n: 显示名字

    su(switch user)
        su - USERNAME;登陆式切换
        su USERNAME;非登陆式切换，不会读取用户的配置文件初始化

## 组管理

> *相关文件*
> *1. /etc/passwd*
> *2. /etc/group*

### /etc/group格式
    group_name:password:GID:user_list
### /etc/gshadow格式

    groupadd
        -g
        -r: 创建系统组

    groupmod
        -g
        -n: 修改名字

    groupdel

##权限管理

###权限查看

    ls -l

###权限修改

    类型:
        u: 属主
        g: 属组
        o: 其它
        a: 所有

    chmod

        1. 赋值表示法
            u=
            g=
            o=
            a=

        2. 授权表示法
            u+, u-
            g+, g-
            o+, o-
            a+, a-

        3. 数字表示法
            如777，453

        4. chmod [OPTION]... --reference=SOME FILE
    
        选项:
            -R
    
    chown
    chgrp
    umask
