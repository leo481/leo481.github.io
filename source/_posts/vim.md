---
title: vim
---

前身 vi: visual interface
所以 vim: vi improved

类型
    模块化的编辑器

基本模式
    编辑模式，又叫命令模式
    输入模式
    末行模式

打开文件
    vim [OPTIONS] [FILE..]
        +#: 打开后，光标的地方
        +/PATTEN: 打开后，光标置于匹配到的行

模式转换
    默认是编辑模式

    编辑模式 --> 输入模式
        i: 光标处输入
        a: 光标后输入
        o: 光标下打开新行
        O: 光标上打开
        I: 行首输入
        A: 行尾输入

    输入模式 --> 编辑模式
        ESC

    编辑模式 --> 末行模式
        :

    末行模式 --> 编辑模式
        ESC

关闭文件
    编辑模式下
    ZZ
    :wq 保存并退出
    :q 退出
    :q! 不保存退出
    :x 保存并退出
    :w /PATH/TO/SOMEWHERE 另存为 

光标跳转
    字符间跳转
        h: 左
        j: 下
        k: 上
        l: 右

        #COMMAND: 跳转多个字符

    单词间跳转
        w: 下一单词词首
        e: 当前或后一个单词词尾
        b: 当前或前一个单词词首

    行首行尾跳转
        ^: 行首
        $: 行尾

    行间跳转
        G: 最后一行
        1G, gg: 第一行
        #G: 指定行 

    翻屏
        ctrl+f: 向后翻一屏
        ctrl+b: 向前
        ctrl+d: 向后翻半屏
        ctrl+f: 向前
        enter: 向后翻一行

    编辑命令

        字符编辑
            x: 删除当前光标字符
            #x: 
            xp: 交换光标与后一个字符的位置

        字符替换
            r

        删除命令,相当于剪切
            d: 
            d$: 
            d^: 
            dw: 删除一个词
            de:
            db:
            dd:
            #dd: 

        复制命令(yank)
            y: 复制当前
            ...同d命令

        粘贴命令(paste)
            p: 粘贴到光标后或行下方
            P: 粘贴到光标前或行上方

        改变命令(change)
            删除后进入输入模式
            c..同d

        可视化模式
            v: 按字符选定
            V: 按行选定

            结合编辑命令使用: d, c, y

        撤销命令(undo)
            u: 撤销一次操作
            #u: 撤销多次
            
        重复操作
            .: 重复一次
        
    末行模式

        地址定界
            :start_pos[,end_pos]
                #: 
                .: 当前
                $: 最后一行
                #,#:
                #,+#: 
                %: 全文
                /pattern/: 光标处到行尾第一次匹配到的行
                /pat1/,/pat2/: 
            
            联合编辑命令一起使用
                d
                y
                c
                w /PATH/TO/SOMEWHERE: 保存范围内文本到指定文件中
                r /PATH/FROM/SOMEWHERE: 从指定文件中读取并插入到指定位置
            
        查找
            /PATTERN: 光标处到尾部所有匹配
            ?PATTERN: 光标到首部所有匹配

        替换
            s/要查找的内容/替换的内容/修饰符
                替换的内容: 可以用&关键字引用前面分组的文本
                修饰符:
                    i: 忽略大小写
                    g: 全局替换
            
            可把分隔符替换为其它非常用字符:
                s@@@
                s###
            
    多文件功能:
        vim FILE1 FILE2
            :next 下一个
            :prev 上一个
            :first 第一个
            :last 最后一个

        退出:
            :wqall 保存并退出所有文件

        多窗口
            -o: 水平分割
            -O: 垂直分割

            切换: ctrl+w,arrow

            单文件分割
                ctrl+w, s: 水平分割
                ctrl+w, v: 垂直分割

    额外选项:
        1. 显示行号
                set nu
                set nonu
        2. 括号匹配高亮
                set sm
                set nosm
        3. 自动缩进
                set ai
                set noai
        4. 语法高亮
                syntax on
                syntax off
    