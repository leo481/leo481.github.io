---
title: Linux的文件系统(FHS)
---

* /bin: 所有用户可用的基本命令程序文件
* /sbin: 供系统管理使用的工具程序
* /lib: 为系统启动或跟文件系统上的应用程序(/bin,/sbin等)提供共享库，以及为内核提供内核模块
    libc.so.*: 动态链接的C库
    ld*: 运行时链接器/加载器
    modules: 用于存储内核模块的目录
* /lib64: 64位系统特有的存放64位共享库的路径
* /boot: 引导加载器必须用到的静态文件; kernel, initramfs(initr), grub等
* /etc: editable text configuration, 系统程序的配置文件，只能是静态
* /dev: 存储特殊文件或设备文件(字符设备，块设备)
* /usr: unix system resource, 存放各种程序和数据
    bin, sbin: 所有可执行文件
    lib, lib64: 所有可执行文件需要到的库
    include: C程序头文件，编译时需要用到
    share: 命令手册和自带文档
    local: 存放手动安装的程序, 跟/usr同一种层级结构
    src: 程序源码文件
    X11R6: X-Window
* /root: 管理员家目录
* /home: 普通的家目录，每个用户都有一个子目录
* /mnt: 其它文件系统的临时挂载点
* /media: 便携式设备挂载点，如cdrom
* /opt: 附加应用程序的安装路径
* /srv: 当前主机为服务提供的数据
* /tmp: 临时存储文件的目录，所有用户可以访问
* /var: 存储经常发生彼岸花的数据的目录
* /proc: 基于内存的虚拟文件系统，用于为内核及进程存储其相关信息；多为讷河参数，例如net.ipv4/ip_forward, 存储在/proc/sys/
* /sys: sysfs虚拟文件系统提供了一种比proc更为理想的访问内核数据的途径；作用在于管理Linux设备提供同意模型的接口
