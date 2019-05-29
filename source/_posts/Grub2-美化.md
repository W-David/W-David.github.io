---
title: Grub2 美化
date: 2019-05-29 22:06:46
tags: grub
---

## 1.找资源
	个人认为比较好看的一款，Aurora-Penguinis-GRUB2;
	下载，解压
	温习一下解压常用的命令：
	* tar格式：
		× 解包：tar xvf filename.tar
		× 压缩：tar cvf filename
	* tar.gz 格式：
<!-- more -->
		× 解包：tar zxvf filename.tar.gz
		× 压缩：tar zcvf filename.tar.gz
	* zip格式：
		× 解包：unzip filename.zip
		× 压缩：zip filename.zip
## 2.开始配置
1.	将解压好的文件夹复制到/boot/grub/themes文件夹下:
	`sudo cp -r Aurora-Penguinis-GRUB2 /boot/grub/themes`
2.	设置这个主题为要使用的：
	`sudo vim /etc/defalut/grub`
3.	在在这个文件中修改GRUB_THEME的值：
	`GRUB_THEME="/boot/grub/themes/Aurora-Penguinis-GRUB2/theme.txt"`
4.	更新Grub,让配置生效：
	`grub-mkconfig -o /boot/grub/grub.cfg`


