---
title: PicGo+GitHub配置图床
date: 2022-03-07 11:45:37
categories:
- 个人博客
comments: true
---

## 下载[PicGo](https://github.com/Molunerfinn/PicGo/releases) 并配置好

<!-- more -->

![image-20220307234822113](https://raw.githubusercontent.com/ZhangWei2222/PictureBed/master/img/202203072348153.png)



## 生成 github token

点击生成之后Token只会出现一次，记得保存备用

<img src="https://raw.githubusercontent.com/ZhangWei2222/PictureBed/master/img/202203072349109.png" alt="img" style="zoom: 33%;" />

<img src="https://raw.githubusercontent.com/ZhangWei2222/PictureBed/master/img/202203072349793.png" alt="在这里插入图片描述" style="zoom:33%;" />

<img src="https://raw.githubusercontent.com/ZhangWei2222/PictureBed/master/img/202203072349672.png" alt="img" style="zoom:33%;" />

<img src="https://raw.githubusercontent.com/ZhangWei2222/PictureBed/master/img/202203072349655.png" alt="img" style="zoom:33%;" />

<img src="https://img-blog.csdnimg.cn/5b38543cd03b4418980b586628222fe7.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBA5oiR5oOz5YW75Y-q5rGk5aeG54yr,size_20,color_FFFFFF,t_70,g_se,x_16" alt="img" style="zoom:33%;" />



## 在PicGo中设置Github图库

![image-20220307235049754](https://raw.githubusercontent.com/ZhangWei2222/PictureBed/master/img/202203072350790.png)

- 设定仓库名：Github账户名/仓库名

- 设定分支名：master

- 设定Token：上面复制的

- 指定储存路径：img/

- 设定自定义域名：https://raw.githubusercontent.com/(在这里填入的用户名)/仓库名/分支名（https://raw.githubusercontent.com/ZhangWei2222/PictureBed/master）

> 自定义域名的作用是，在上传图片后成功后，PicGo会将“自定义域名+上传的图片名”生成的访问链接，放到剪切板上https://raw.githubusercontent.com/用户名/RepositoryName/分支名，，自定义域名可按此填写。

- 填写完毕确认并设置为默认图库
  

## 配置 Typora

<img src="https://raw.githubusercontent.com/ZhangWei2222/PictureBed/master/img/202203072352672.png" alt="image-20220307235256611" style="zoom:33%;" />