# CentOS-Kernel-412

CentOS kernel mainline v4.12 backup.


# tcp_tsunami
tcp_tsunami adjust for kernel 4.12↓（魔改版bbr，兼容4.09-4.12内核）

## How to use

### Update your kernel to 4.12 and make the default boot order (KVM CentOS7)
1. Remove previous headers
	`rpm -e --nodeps kernel-ml-headers`
  
2. Install kernel 4.12
	```
	yum install git -y
	git clone -b master https://www.github.com/stardock/CentOS-Kernel-412
	cd CentOS-Kernel-412
	rpm -ivh kernel-ml-4.12.10-1.el7.elrepo.x86_64.rpm
	rpm -ivh kernel-ml-devel-4.12.10-1.el7.elrepo.x86_64.rpm
	rpm -ivh kernel-ml-headers-4.12.10-1.el7.elrepo.x86_64.rpm
	```
3. Check the boot order and make default boot
	`awk -F\' '$1=="menuentry " {print i++ " : " $2}' /etc/grub2.cfg`
	`grub2-set-default 0`

### Install tsunami

1. Install `make gcc`. 安装`make gcc`。
2. Run 执行:
	```
	wget -O ./tcp_tsunami.c https://gist.github.com/anonymous/ba338038e799eafbba173215153a7f3a/raw/55ff1e45c97b46f12261e07ca07633a9922ad55d/tcp_tsunami.c
	echo "obj-m:=tcp_tsunami.o" > Makefile
	make -C /lib/modules/$(uname -r)/build M=`pwd` modules CC=/usr/bin/gcc
	insmod tcp_tsunami.ko
	cp -rf ./tcp_tsunami.ko /lib/modules/$(uname -r)/kernel/net/ipv4
	depmod -a
	modprobe tcp_tsunami
	echo "net.core.default_qdisc=fq" >> /etc/sysctl.conf
	echo "net.ipv4.tcp_congestion_control=tsunami" >> /etc/sysctl.conf
	sysctl -p
	```
4. Check 检查 `sysctl net.ipv4.tcp_congestion_control`

