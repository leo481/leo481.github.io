---
title: find详解
date: 2018-01-22 17:36:41
tags:
---

简介:实时查找工具,通过遍历文件系统层级结构完成文件查找

特性:
    查找速度略慢(相对于locate)
    精确查找
    实时查找

用法:
    find [OPTIONS] [START_POS] [CONDITION] [ACTION]

    CONDITION

        根据文件名
            -name "pattern"
            -iname "pattern"
                支持*,?,[],[^]通配符

        根据从属
            -user USERNAME
            -group GROUPNAME

            -uid UID
            -gid GID

            -nouser
            -nogroup

        根据文件类型
            -type TYPE(f,d,l,b,c,p,s)

        可使用与(-a),或(-o),非(!)组合条件

        根据文件大小
            -size [+|-]#UNIT
            常用单位:k M G

        根据时间戳
            以天为单位
                -atime [+|-]#
                -mtime
                -ctime

            以分钟为单位
                -amin
                -mmin
                -cmin

        根据权限
            -perm [/|-]mode
                mode: 精确匹配
                /mode: 满足其一即可
                -mode: 同时满足
            example:
                查找/etc目录下所有用户都没有写权限的文件；
                        ~]# find /etc -not -perm /222 -type f -ls	

        ACTION
            -print: 默认动作,输出到屏幕
            -ls: 输出文件的详细信息
            -delete: 删除
            -fls SOMEWHERE: 把文件的信息保存到文件
            -ok COMMAND {} \: 对每个查找到的文件执行COMMAND,每个文件都需要用户确认
            -exec COMMAND {} \:对每个查找到的文件执行COMMAND