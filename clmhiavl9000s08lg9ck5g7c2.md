---
title: "Mac 外置硬盘扩展方案"
datePublished: Wed Sep 13 2023 08:55:22 GMT+0000 (Coordinated Universal Time)
cuid: clmhiavl9000s08lg9ck5g7c2
slug: mac
tags: macos

---

主要分为四大类，1. 最方便可靠的方案更换硬盘芯片，价格 1000元/T；2. 外接移动硬盘；3. Nas。 #Mac

# 外接移动硬盘

设备是Mac Mini，没有移动、携带的需求，所以考虑这种性价比最高的方案。

## 在移动硬盘中安装Mac OS

## 将应用、数据存放在移动硬盘中

**Mac将应用程序及其数据移到外接硬盘的步骤**

首先要明确 Mac上的应用程序分dmg安装包和ipa安装包，ipa在安装时可以选择安装路径，而dmg安装包只能装到Applications文件夹。所以以下步骤适用于dmg安装。

应用程序除了本体以外，还有数据文件。有的应用程序本体比较大，如Matlab，有的数据文件比较大，如Wechat。可以选择性的移动本体还是数据文件。 移动应用程序本体。 在“应用程序”文件夹将应用程序图标拖到外接硬盘中即可。 建议在这一步之后从外接硬盘打开一下应用程序，看是否能正常启动，有的应用程序要求必须安装在系统的应用程序文件夹中，此时启动会提示，如果要求必须安装在系统应用程序文件夹，则跳过这一步。如果提示已损坏，看下文注意事项。 移动数据文件。 数据文件一般需要在应用程序中右键点在访达中查看，然后右键显示简介，即可看到位置信息。 （数据文件一般存在于/User/username/Library或其下的Container文件夹中。如果找不到可以去里面找）

> 该方案针对Container的文件夹不一定有效，软链接以后会出现无法正常读写的问题。参考下面操作方式\[\[#将Containers 挂在到移动硬盘\]\]

首先将需要移动的文件/文件夹移动到外接硬盘（复制粘贴，然后删除源文件）；

然后在终端（terminal）输入以下命令

```shell
ln -s 现在的数据文件 原来的路径
```

### 替身与软链接

Mac系统上有软链接、硬链接、替身的概念，此处就不加赘述。 我关注到替身和软链接的区别： 替身是Mac的概念，且是finder中的概念，在其他系统无法识别；软链接是Linux的概念，通用性高。 替身文件在某些软件中无法正常识别；软链接都可以。具体看软件的文件读取协议。 软链接在创建时会在finder中显示为一个替身。

## 将Containers 挂在到移动硬盘

**经测试有效**

OK, finally I solved this problem. My solution:

1. Use Disk [Utility.app](http://Utility.app) to create a new blank image with sparseimage type and save it at the external ssd drive. 使用磁盘管理工具，在移动硬盘中新建一个分区
    
    * *稀疏磁盘映像：*创建可按需收缩和扩大的可扩展文件。不会占用额外的空间。使用 .sparseimage 文件扩展名。
        
2. Then mount the sparse image at the target location, such as hdiutil attach /Volumes//file.sparseimage -mountpoint ~/Library/Containers/&lt;target\_path&gt; 将分区挂载到~/Library/Containers/ 目录下的 软件名目录，如 com.tencent.wechat
    
3. move all the original files to the mounted point. 将原来的文件移动到刚才挂在的目录
    
4. 在fstab中写入刚才的挂载路径，这样每次开机以后都会自动挂载
    

That's all. By the way, you need to quit the app first and rename the target directory to something else before mounting the image obviously.

One more tip: you should make sure the target directory you want to move will not have any broken symbolic links after this workaround by running find . -type l -ls command.