---
title: bash shell的字符串处理(转)
date: 2018-02-06 16:56:44
tags:
---

## 字符串切片，对于变量而言 
例如：name=nihaoma , echo ${name:2:3} 打印的结果为：hao
例如：name=nihaoma , echo ${name: -4} 打印的结果为：aoma
## 基于模式取字符串，对于变量而言 
 - ${var#\*word}：其中word是指定的分隔符。功能：自左向右，查找var变量所存储的字符串中，第一次出现的word分隔符，删除字符串开头至此分隔符处的所有字符 
例如：path="/var/log/yhy.txt" , echo ${path#\*/} 打印结果为：var/log/yhy.txt
 - ${var##\*word}：其中word是指定的分隔符。功能：自左向右，查找var变量所存储的字符串中，最后一次出现的word分隔符，删除字符串开头至此分隔符处的所有字符 
例如：path="/var/log/yhy.txt" , echo ${path##\*/} 打印结果为：yhy.txt
 - ${var%word\*}：其中word是指定的分隔符。功能：自右向左，查找var变量所存储的字符串中，第一次出现的word分隔符，删除字符串结尾至此分隔符处的所有字符 
例如：path="/var/log/yhy.txt" , echo ${path%/\*} 打印结果为：/var/log
 - ${var%%word\*}：其中word是指定的分隔符。功能：自右向左，查找var变量所存储的字符串中，最后一次出现的word分隔符，删除字符串结尾至此分隔符处的所有字符 
例如：path="/var/log/yhy.txt" , echo ${path%%/\*} 打印结果为：空
## 查找替换 
 - ${var/pattern/substi}：查找var所表示的字符串，第一次被pattern所匹配到的字符串，将其替换为substi所表示的字符串
 - ${var//pattern/substi}：查找var所表示的字符串，所有被pattern所匹配到的字符串，将其所有匹配到的字符串替换为substi所表示的字符串
 - ${var/#pattern/substi}：查找var所表示的字符串，行首被pattern所匹配到的字符串，将其替换为substi所表示的字符串，如果不是行首，就不替换
 - ${var/%pattern/substi}：查找var所表示的字符串，行尾被pattern所匹配到的字符串，将其替换为substi所表示的字符串，如果不是行尾，就不替换 
## 查找删除
 - ${var/pattern}：查找var所表示的字符串，第一次被pattern所匹配到的字符串删除
 - ${var//pattern}：查找var所表示的字符串，所有被pattern所匹配到的字符串删除
 - ${var/#pattern}：查找var所表示的字符串，行首被pattern所匹配到的字符串删除
 - ${var/%pattern}：查找var所表示的字符串，行尾被pattern所匹配到的字符串删除
## 字符大小写转换 
 - ${var^^}：把var中的所有小写转换为大写
 - ${var,,}：把var中的所有大写转换为小写
## 变量赋值 
 - ${var:-VALUE}: 如果var变量为空，或未设置那么返回VALUE，否则返回var变量的值
 - ${var:=VALUE}: 如果var变量为空，或未设置那么返回VALUE，并将VALUE赋值给var变量，否则返回var变量的值
 - ${var:+VALUE}: 如果var变量不空，那么返回VALUE
 - ${var:?ERROR_INFO}: 如果var为空，或未设置，那么返回ERROR_INFO为错误提示，否则，返回var的值