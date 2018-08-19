---
layout:     post
title:      "Emacs"
date:       2018-08-19
author:     "Chanrom"
header-img: ""
catalog:    true
comment: false
tags:
    - emacs
---


# 部分参考如下网站
[Emacs常用快捷键](https://www.cnblogs.com/kunyuanjushi/p/5951404.html)


# 基本命令

```
C-x C-f     打开/新建文件
C-x C-s     保存当前缓冲区
C-x C-w     当前缓冲区另存为
C-x b       切换Buffer
C-x C-b     显示Buffer列表
C-x k       关闭当前Buffer
C-x C-v     关闭当前Buffer并打开新文件
C-x C-c     关闭Emacs
C-c C-z     终止shell中的进程
```

# 窗口命令

```
C-x 2       水平分割窗格
C-x 3       垂直分割窗格
C-x 0      关闭当前窗口
C-x o      切换窗口
C-x 1       关闭其他窗口
C-x 5 2   新建窗口
C-x 5 f    新窗口中打开文件
```

# 光标移动命令

```
C-x 2       水平分割窗格
C-x 3       垂直分割窗格
C-x 0      关闭当前窗口
C-x o      切换窗口
C-x 1       关闭其他窗口
C-x 5 2   新建窗口
C-x 5 f    新窗口中打开文件
```

# 编辑命令

```
C-v              向前翻页
M-v              向后翻页
M-r              将光标移动到屏幕中间那行
C-a              移到行首
M-a              移到句首，从行首到句首之间可能有空格
C-e              移到行尾
M-e              移到句尾
M-{              向上移动一段
M-}              向下移动一段
C-right              向前移动一个单词
C-left              向后移动一个单词
C-up              向前移动一段
C-down              向后移动一段
M-<              移到整个文本开头
M->              移到整个文本末尾
M-x goto-line              移动到某一行
C-l              重绘屏幕，效果就是当前编辑行移动窗口中央
C-Space         设置开始标记
C-@                设置开始标记(C-space可能被操作系统拦截)
M-w                复制标记区内容
C-y                  帖粘
M-u                使光标处的单词大写
M-l                 使光标处的单词小写
M-c                使光标处单词首字母大写
C-k                  删除一行
```


# 搜索/替换命令

```
C-s          向下搜索
C-r          向上搜索
M-x replace-string 替换（替换光标之后的内容）
M-x replace-regexp 正则替换（替换光标之后的内容）
```

# 帮助

```
C-h ?              查看帮助信息
C-h f              查看一个函数
C-h v              查看一个变量
C-h k              查看一个键绑定 (C－h c 也是查看键绑定，但是信息较简略)
C-h C-f              查看一个函数的info，非常有用
C-h i              看Info
```


# 其他

```
C-M-\              对选中区域，按照某种格式(比如C程序)进行格式化
C-x h              全部选中
M-!              执行外部shell命令
M-x shell              模拟shell的buffer
M-x term              模拟terminal, C-c k 关闭terminal
C-x C-q              修改buffer的只读属性
```