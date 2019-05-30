---
title: Vim-初步
thumbnail: /gallery/wallhaven-747189.jpg
date: 2019-05-27 21:16:45
tags: vim
---
## vim 模式：
	vim有四个模式； 分别是
	编辑模式：按ESC 进入
	输入模式：i/a/o 或者 I/A/O
	命令行模式：按：进入
	选择模式：按 v 进入
<!-- more -->

## vim 常用命令
	* w : 移动到下一个单词开头
	* e : 移动到下一个单词结尾
	* b : 移动到上一个单词开头

	* f {char} 移动到下一个char字符上 / F （上一个）
	* 使用；或者，继续移动到下一个/上一个char

	* G : 移动到文本结束行开头
	* gg: 移动到文本开始行开头

	* ct" : 删除从当前位置到“字符之间的内容
	* ci{ : 修改{}内的内容

	* /{word} ： 搜索word
	* daw/yaw/caw (dw/yw/cw):删除/复制/更改当前字符
	* diw/yiw/ciw: 删除/复制/更改当前字符（不包括周围的空白符）
	
	* gi : 光标跳回到上一次编辑的位置并进入插入模式
	
	* :saveas <filename> : 保存为filename
	* :e <filename> : 打开一个文件
	* :wq :保存并离开
## vim 高级用法：
	**未解锁** 


