# 概述
linux下使用ntfs-3g挂载NTFS

备注：
* 稳定性问题：CentOS 7.9上安装ntfs-3g，然后挂载一个4T的硬盘下载ppt，下载2T左右（12小时左右）的时候磁盘报错，不能访问;

# 前提
* 安装ntfs-3g: ```yum install -y ntfs-3g```;

# 挂载命令
* 使用ntfs方式挂载硬盘，例如：```mount -t ntfs-3g /dev/xvdb5 /mnt```;

# 自动挂载
在/etc/fstab里面添加如下配置：
* 只读方式挂载：echo "/dev/xvdb5 /mnt ntfs-3g ro,umask=0222,defaults 0 0" >> /etc/fstab；
* 读写方式挂载：echo "/dev/xvdb6 /mnt ntfs-3g rw,umask=0000,defaults 0 0" >>/etc/fstab；
