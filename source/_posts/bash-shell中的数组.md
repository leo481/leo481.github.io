---
title: bash shell中的数组
date: 2018-02-06 12:23:49
tags:
---


如果以前学过java,c#或者javascript等高级语言的话,那么bash shell中的数组操作与其有一定的差距,下面来熟悉一下Bash Shell的数组
##声明数组
```
declare  -a  NAME：声明索引数组；
declare  -A  NAME：声明关联数组；
```
##赋值方式
1. 一次只赋值一个元素；  
    ARRAY_NAME[INDEX]=value
2.  一次赋值全部元素；  
    ARRAY_NAME=("VAL1"  "VAL2"  "VAL3"  ...)
3. 只赋值特定元素；  
    ARRAY_NAME=([0]="VAL1"  [3]="VAL4" ...)

##引用元素
        ${ARRAY_NAME[INDEX]}
##数组长度
```
${#ARRAY_NAME[\*]}
${#ARRAY_NAEM[@]}
```
##数组所有元素
        ${ARRAY_NAME[\*]}
        ${ARRAY_NAME[@]}
##数组部分元素
        ${ARRAY_NAME[\*]:offset:number}
##删除元素
        unset ARRAY[INDEX]
