---
title: wallabysnightmare102-writeup
date: 2023-11-24 19:14:58
tags:
    - 🌟
    - writeup
categories:
    - 网络安全
---

# wallabysnightmare102_writeup
这是学校作业
<!--more-->
# 靶机简介
> 靶机名：wallabysnightmare102
> 靶机链接：https://www.vulnhub.com/entry/wallabys-nightmare-v102,176/
> 作者博客：https://www.arashparsa.com/

# 攻击流程

## 信息搜集
1. 80端口web服务
2. 使用nikto扫描漏洞后会关闭80端口，并吧网站转移到60080端口
## getshell
1. 发现page参数具有目录穿越漏洞
2. nikto扫描该参数
3. 80端口关闭，继续扫描60080端口的page参数
4. 发现了/?page=mailer网页注释
5. 发现/?page=mailer&mail=ls命令执行漏洞
6. 通过metasploit获取php reverse shell
## 提权
1. 根据内核版本，上传脏牛提权漏洞 https://gist.githubusercontent.com/rverton/e9d4ff65d703a9084e85fa9df083c679/raw/9b1b5053e72a58b40b28d6799cf7979c53480715/cowroot.c
2. getroot

# 工具和技术
> [[nikto]] web 漏洞扫描工具
> metasploit 获取反弹shell用
> `python3 -m http.server 8081` 开启简易web网站，用于上传提权脚本

# 渗透过程及结果

1. 端口扫描
![](wallabysnightmare102-writeup/1.png)
2. 浏览网站
![](wallabysnightmare102-writeup/2.png)
![](wallabysnightmare102-writeup/3.png)
![](wallabysnightmare102-writeup/4.png)
3. 发现目录穿越漏洞
![](wallabysnightmare102-writeup/5.png)
4. 通过nikto进行漏洞扫描，但是扫到一半超时了
![](wallabysnightmare102-writeup/6.png)
5. 发现开启了新端口
![](wallabysnightmare102-writeup/7.png)
![](wallabysnightmare102-writeup/8.png)
6. 访问网站
![](wallabysnightmare102-writeup/9.png)
7. 发现网站仍然有page参数
![](wallabysnightmare102-writeup/10.png)
8. 遍历page参数值
![](wallabysnightmare102-writeup/11.png)
9. 发现mailer有命令执行漏洞
![](wallabysnightmare102-writeup/12.png)
![](wallabysnightmare102-writeup/13.png)
![](wallabysnightmare102-writeup/14.png)
10. 远程连接
![](wallabysnightmare102-writeup/15.png)
![](wallabysnightmare102-writeup/16.png)
11. 上传提权脚本
![](wallabysnightmare102-writeup/17.png)
![](wallabysnightmare102-writeup/18.png)
12. 编译脚本
![](wallabysnightmare102-writeup/19.png)
13. getshell
![](wallabysnightmare102-writeup/20.png)
  

# 总结反思
1. 要善于使用工具，发挥工具的最大功用；当然，弄清楚原理、学的深入是最重要的
2. metasploit是一个功能强大的软件，这意味着要学习如何使用也是很花时间的，这也是确实是未来需要花的
# 参考资料
> 学校给的资料
> 提权脚本 https://gist.githubusercontent.com/rverton/e9d4ff65d703a9084e85fa9df083c679/raw/9b1b5053e72a58b40b28d6799cf7979c53480715/cowroot.c