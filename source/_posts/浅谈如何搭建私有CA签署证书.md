---
title: 浅谈如何搭建私有CA签署证书
date: 2018-02-06 22:22:42
tags:
---

在现今电子商务越来越发达的时代中,交易安全是其中一个重要的环节,而CA则是交易安全的实现

我们来看看维基百科中对CA(数字证书认证机构)的介绍
![CA介绍](https://hexo-1256034174.cos.ap-guangzhou.myqcloud.com/CA%E4%BB%8B%E7%BB%8D.png)

> 实验环境:
> 2台CentOS主机,1台用作签署CA,另1台用作申请签署

## CA工作目录
/etc/pki/tls/openssl.cnf中有CA默认的工作目录说明
```
dir             = /etc/pki/CA           # Where everything is kept
certs           = $dir/certs            # Where the issued certs are kept
crl_dir         = $dir/crl              # Where the issued crl are kept
database        = $dir/index.txt        # database index file.
#unique_subject = no                    # Set to 'no' to allow creation of
                                        # several ctificates with same subject.
new_certs_dir   = $dir/newcerts         # default place for new certs.

certificate     = $dir/cacert.pem       # The CA certificate
serial          = $dir/serial           # The current serial number
crlnumber       = $dir/crlnumber        # the current crl number
                                        # must be commented out to leave a V1 CRL
crl             = $dir/crl.pem          # The current CRL
private_key     = $dir/private/cakey.pem# The private key
RANDFILE        = $dir/private/.rand    # private random number file
```
## 建立CA服务器
### 生成密钥文件
```
cd /etc/pki/CA
(umask 066;openssl genrsa -out /etc/pki/CA/private/cakey.pem 2048)
```
![](https://hexo-1256034174.cos.ap-guangzhou.myqcloud.com/Snipaste_2018-02-06_23-08-13.png)
### 生成自签名证书
```
openssl req -new -x509 -key /etc/pki/CA/private/cakey.pem -days 7300 -out /etc/pki/CA/cacert.pem
```
- req：请求申请证书
- -new: 生成新证书签署请求
- -x509: 专用于CA 生成自签证书
- -key: 生成请求时用到的私钥文件
= -days n ：证书的有效期限
- -out / PATH/TO/SOMECERTFILE : 生成私钥证书的保存路径
![](https://hexo-1256034174.cos.ap-guangzhou.myqcloud.com/Snipaste_2018-02-06_23-12-09.png)
### 初始化工作环境
```
touch /etc/pki/CA/index.txt 生成证书索引数据库文件
touch /etc/pki/CA/serial
echo 01 > /etc/pki/CA/serial 指定第一个颁发证书的序列号，一般都是从01开始
mkdir csr   用来放置请求文件
```
## 申请证书
切换到另一台机器上
### 生成私钥
```
(umask 066;openssl genrsa -out /app/service.key 2048)
```
![](https://hexo-1256034174.cos.ap-guangzhou.myqcloud.com/Snipaste_2018-02-06_23-22-35.png)
### 利用上面生成的私钥,创建证书请求文件
```
openssl req -new -key /app/service.key -days 365 -out /app/service.csr
```
![](https://hexo-1256034174.cos.ap-guangzhou.myqcloud.com/Snipaste_2018-02-06_23-27-06.png)
### 把此请求文件发给CA机器的/etc/pki/CA/csr文件夹中(上面手动生成的)

### 签署证书
```
openssl ca -in /etc/pki/CA/csr/service.csr -out /etc/pki/CA/certs/service.crt -days 1000
```
![](https://hexo-1256034174.cos.ap-guangzhou.myqcloud.com/Snipaste_2018-02-06_23-38-47.png)
 ### 颁发证书
 把签署好的证书发还给申请者,即完成了签署
 ## 吊销证书
### 在客户端（A端）获取要吊销的证书的serial（编号）
```
openssl x509 -in / PATH/FROM/CERT_FILE -noout -serial -subject
```
### 在CA（B端） 上，根据客户提交的serial 与subject 信息，对比检验是否与index.txt 文件中的信息一致，确认就吊销证书：
```
openssl ca -revoke newcerts/01.pem
```
### 指定第一个吊销证书的编号

> 注意：第一次更新证书吊销列表前，才需要执行
```
echo 01 > /etc/pki/CA/crlnumber
```
### 更新证书吊销列表，将来将吊销的列表放到互联网上，让大家知道
```
openssl ca -gencrl -out /etc/pki/CA/crl/crl.pem
```