---
title: rpm包管理
date: 2018-01-25 23:44:46
tags:
---

最初,许多Linux程序以源代码的形式发行,用户再把源代码通过预处理,编译,汇编,链接构建成程序.另外还有一系列的帮助文档以及配置文件.如今发行商使用包的构建的程序或程序集,包便于在发行版上安装.

Red Hat开发的Red Hat Package Manager(RPM),以及Debian的APT是如今主流的两个包管理系统.它们都可以安装和删除包.包的信息都保存在数据库中.它们都有相似的基本操作指令,同时通过其它工具提供对用户友好的界面.

# RPM

Red Hat 在1995年引入了RPM.RPM现在是Linux Standard Base(LSB)中采用的包管理系统.rpm命令选项分为三类:

    - 查询和升级包
    - 安装,升级和卸载包
    - 其它功能

# YUM

YUM在RPM系统中增加了自动更新和包管理,还提供了依赖关系的管理.
除了了解系统上安装的包之外,YUM还使用存储库.存储库是包的集合,通常可以通过网络连接访问它们.

# 安装RPM包

首先,尝试安装一个gcl
```
[root@echidna ~]# rpm -i gcl-2.6.8-0.6.20090701cvs.fc12.x86_64.rpm
error: Failed dependencies:
gcl-selinux is needed by gcl-2.6.8-0.6.20090701cvs.fc12.x86_64
```

rpm知道这个包依赖于另外一个包,但是rpm并不会解决这个问题.于是你需要把这个被依赖的包安装,然后再次尝试.如果这个包还依赖于更多的包的话,需要不断重复上述过程,直至满足所有依赖关系为止.如果一开始把所有的包都提供的话,rpm将会以正确的次序安装所有包.

此时,yum的出现可以大大减少这方面的工作量.它会自动徐照需要的依赖包并安装它们

```
[root@echidna ~]# yum install gcl
Loaded plugins: presto, refresh-packagekit
Setting up Install Process
Resolving Dependencies
--> Running transaction check
---> Package gcl.x86_64 0:2.6.8-0.7.20100201cvs.fc12 set to be updated
--> Processing Dependency: gcl-selinux for package: gcl-2.6.8-0.7.20100201cvs.fc12.x86_64
--> Running transaction check
---> Package gcl-selinux.x86_64 0:2.6.8-0.7.20100201cvs.fc12 set to be updated
--> Finished Dependency Resolution
 
Dependencies Resolved
 
=====================================================================================
 Package           Arch         Version                          Repository     Size
=====================================================================================
Installing:
 gcl               x86_64       2.6.8-0.7.20100201cvs.fc12       updates       6.3 M
Installing for dependencies:
 gcl-selinux       x86_64       2.6.8-0.7.20100201cvs.fc12       updates        17 k
 
Transaction Summary
=====================================================================================
Install       2 Package(s)
Upgrade       0 Package(s)
 
Total download size: 6.4 M
Installed size: 40 M
Is this ok [y/N]: y
Downloading Packages:
Setting up and reading Presto delta metadata
updates/prestodelta                                           | 964 kB     00:01     
Processing delta metadata
Package(s) data still to download: 6.4 M
(1/2): gcl-2.6.8-0.7.20100201cvs.fc12.x86_64.rpm              | 6.3 MB     00:12     
(2/2): gcl-selinux-2.6.8-0.7.20100201cvs.fc12.x86_64.rpm      |  17 kB     00:00     
-------------------------------------------------------------------------------------
Total                                                398 kB/s | 6.4 MB     00:16     
Running rpm_check_debug
Running Transaction Test
Transaction Test Succeeded
Running Transaction
  Installing     : gcl-selinux-2.6.8-0.7.20100201cvs.fc12.x86_64                 1/2 
  Installing     : gcl-2.6.8-0.7.20100201cvs.fc12.x86_64                         2/2 
 
Installed:
  gcl.x86_64 0:2.6.8-0.7.20100201cvs.fc12                                            
 
Dependency Installed:
  gcl-selinux.x86_64 0:2.6.8-0.7.20100201cvs.fc12                                    
 
Complete!
```

输出表明,YUM在名为"update"的存储库中找到了gcl.x86_64 0:2.6.8-0.7.20100201cvs.fc12 和 gcl-selinux.x86_64 0:2.6.8-0.7.20100201cvs.fc12.

## 常用选项

 * -i: 安装
 * -v: 显示安装过程
 * -h: 显示安装进度
 * --replacepkgs: 如果原有配置文件作了修改，很有可能不执行替换，而是将应该安装生成的配置文件重命名为 .rpmnew

# 包的位置

YUM是如何知道从哪里下载包的呢?

起点是/etc/yum.repos.d/这个目录,这个目录通常包含几个repo文件.这是repo的默认位置.你也可以通过修改/etc/yum.conf指定其它位置.

存储库提供了存储库位置以及指出存储库的启用与否,还有是否应该使用gpg签名检查下载的包

# 删除包

与安装包不同，当使用 rpm 删除包时，在删除包之前没有提示。但是，如果试图删除其他包需要的包，它不会执行删除操作.

```
[root@echidna ~]# rpm -e gcl-selinux
error: Failed dependencies:
    gcl-selinux is needed by (installed) gcl-2.6.8-0.7.20100201cvs.fc12.x86_64
```

如果使用 yum remove，在执行事务测试之后会显示提示。如果试图删除的包是其他已安装包的依赖包，YUM 会提议删除这些包和依赖包

```
[root@echidna ~]# yum remove gcl-selinux
Loaded plugins: presto, refresh-packagekit
Setting up Remove Process
Resolving Dependencies
--> Running transaction check
---> Package gcl-selinux.x86_64 0:2.6.8-0.7.20100201cvs.fc12 set to be erased
--> Processing Dependency: gcl-selinux for package: gcl-2.6.8-0.7.20100201cvs.fc12.x86_64
--> Running transaction check
---> Package gcl.x86_64 0:2.6.8-0.7.20100201cvs.fc12 set to be erased
--> Finished Dependency Resolution
 
Dependencies Resolved
 
=====================================================================================
 Package          Arch        Version                            Repository     Size
=====================================================================================
Removing:
 gcl-selinux      x86_64      2.6.8-0.7.20100201cvs.fc12         @updates       90 k
Removing for dependencies:
 gcl              x86_64      2.6.8-0.7.20100201cvs.fc12         @updates       40 M
 
Transaction Summary
=====================================================================================
Remove        2 Package(s)
Reinstall     0 Package(s)
Downgrade     0 Package(s)
 
Is this ok [y/N]: n
Exiting on user Command
Complete!
```
## 常用选项
  * -e: 卸载

# RPM升级
如果知道 RPM 文件的位置，或者已经下载了它们，那么也可以使用 rpm 命令执行更新。这与安装相似，只是要使用 -U 或 -F 选项而不是 -i 选项。这两个选项的差异是， -U 选项更新现有的包，如果这个包还没有安装，就安装它；而 -F 选项只升级或刷新 已经安装的包。因此，经常使用 -U 选项，尤其是在命令行包含 RPM 列表的情况下。这样的话，就会安装未安装的包，升级已经安装的包。

如果使用YUM的话,使用
    yum remove PACKAGENAME

# 查询RPM包

```
rpm –q packages_name #→查询单个指定的包是否安装.
rpm –qa packages_name #→查询所有已安装的包.
rpm –qi packages_name packages_name #→查询指定包的描述信息.
rpm –ql packages_name #→查询包安装之后在当前系统上生成的文件的列表.
rpm –qd packages_name #→查询安装后生成的帮助文档.
rpm –qc packages_name #→查询安装后生成的配置文件.
rpm –q --scripts packages_name #→查询安装后包相关的脚本.
```

# 恢复包
包管理系统通常可以恢复包。但是，如果删除了包中的重要文件，重新安装包但不删除无法解决问题，那么可能需要在重新安装之前删除包。对于这种情况，可能希望删除现有的拷贝并重新安装，但是不希望卸载和重新安装依赖它的所有包。为此，在删除包时，可以使用 rpm 命令的 --nodeps 选项绕过依赖关系检查。

# RPM包的命名格式
name-version-relase.arch.rpm

- version: major.minor.release，同源代码
- release: rpm自身的发行号，与程序源码的发行号无关，仅用于标识对rpm包不同制作的修订；同时，release还包含此包适用的OS
    bash-4.2.3-3.centos5
- arch: 适用于的硬件平台，
    - x86: i386, i486, i586, i686等；
    - x86_64: x86_64
    - powerpc: ppc
    - noarch: 依赖于虚拟机
> 例如：bash-4.2.3-3.centos5.x86_64.rpm

# 获取rpm包的途径：
1. 发行的光盘或站点服务器

    镜像：  
    - http://mirrors.163.com
    -  http://mirrors.sohu.com
2. 项目的官网
    - 源代码
    - rpm包
3. 很多第三方机构或个人制作并公开发布许多rpm包
    - http://rpmfind.net
    -  http://rpm.pbone.net

可靠的途径：EPEL
- Fedora-EPEL
> ### 注意：
>1. 发行商,发行光盘中的包,需要升级时,官方释放出升级包;
>2. 无论是Centos,还是Redhat他在光盘上提供的包并非完全包含我们能使用到的包,于是Fedora 在互联网上维护这一个叫EPEL额外的,分别面向各个版本的rpm包,所以有了发行商提供的光盘和EPEL就能够满足一般工作的需求了.但是如果对于应用场景比较严格的,还是尽量使用光盘中的包.实在不行才使用上面提供的网站去搜索相关的包.

# YUM命令的使用
```
yum repolist [all|enable|available] -> 显示列出所有以配置好的可用yum repo
yum clean[all|packages|metadata|expire-cache|rpmdb|plugins] ->清除缓存
yum [list|installed|available] ->显示所有安装过或可用而未安装但是存储在指定yum仓库中的包
yum info packages_name →列出rpm包的描述
yum grouplist –>列出所有包组
yum groupinfo "Packages_group_name" →显示包组信息(包组名称中含有空格需要加引号)
yum install package_name ...  → 安装指定的程序包
yum reinstall package_name ... -> 重新安装指定的程序包
yum check-update package_name  →检查可升级的包
yum update package_name ... → 升级指定的程序包(若要明确升级到指定的版本,需指定版本号)
yum downgrade package_name → 降级
yum erase|removepackage_name ... → 卸载
yum whatprovides|provides /path/to/somefile:查询某文件是由哪个包安装生成的;与rpm –qf 类似
yum groupinstall "group_name" →安装指定的包组
yum groupremove"group_name" →卸载指定的包组
```