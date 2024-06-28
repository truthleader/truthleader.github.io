---
title: tommyboy-writeup
date: 2023-12-03 12:46:13
tags:
    - 🌟
    - writeup
categories:
    - 网络安全
---

# tommyboy_writeup
这是学校作业
<!--more-->
# 靶机简介
> 靶机名：tommyboy 1
> 靶机作者：[Brian Johnson](https://www.vulnhub.com/author/brian-johnson,256/)
> 靶机链接1：https://www.vulnhub.com/entry/tommy-boy-1,157/
> 靶机链接2:[http://7ms.us/tommyboy](http://7ms.us/tommyboy)
> 目标：5个flag和一个final message
# 攻击思路
## flag1
1. 通过robots.txt泄露发现flag1
## flag2
1. 根据首页源码提示，得到提示链接，知道网站主目录
2. 浏览主目录的posts，发现一篇post的回复包含了flag2存放在什么文件的信息
## flag3
1. 根据主目录post回复中的提示，了解了一个存放图片的网站目录
2. 下载图片，用strings查看，发现了一串密文(一般有一定规律性，让人觉得很规整但看不懂的就是密文)
3. 通过在线md5工具解密该密文，得到一个单词
4. 用该单词作为网站主目录第二篇post的密码，成功访问该post内容
5. 根据该post提示，了解了靶机有一诡异的ftp服务，通过nmap不断扫描，直到发现多处来一个开放端口
6. 通过hydra爆破该服务密码，用户名根据post提示已经知道(记得将用户名添加到字典的第一行)
7. 爆破成功后登入ftp服务
8. 得到提示文本，发现一新目录，但是没讲具体位置，最后在8008端口的web服务上访问到该目录
9. 访问目录的回显提示，只有他和steve jobs可以访问其中的内容，通过脑洞猜测，试出来修改UA为iphone就能访问内容(脑洞什么根本没有的，只有抄的份)
10. 提示需要猜测网站正确的网页名，通过爆破得到网页名，我用的dirbuster(爆破不了一点，100线程就崩了，所以还是抄的)
11. 访问到网站网页，得到flag3和一hint，同时还有一个加密文件
## flag4
1. 根据hint中的提示，生成密码字典，我用的crunch
2. 用fcrackzip爆破成功得到3对用户名和密码，以及一个没有密码的用户名(错的)(前两对密码p用没有，第三对据hint是server的用户名和密码)
3. 通过wpscan扫描网站的用户
4. 对tom用户进行密码爆破，字典是rockyou，用wpscan爆破
5. 访问网站后台，在draft中得到密码提示，与hint中的第三对用户名密码拼接，通过ssh成功得到靶机shell
6. ls发现了flag4
## flag5
1. 根据提示，在靶机根目录发.5.txt
2. 由于5.txt需要www-data权限，需要通过webshell来获得该权限，所以在/var目录下查找网站根目录(由于有两个http端口，所以有两个根目录)
3. 发现可以在/var/thatsg0nnaleaveamark/NickIzL33t/P4TCH_4D4MS/uploads写入webshell
4. 写入一句话木马，通过浏览器直接访问，用该webshell查看.5.txt内容
5. 拼接5个flag，解压loot.zip，通关该靶场
## getroot
1. 上传linpeas脚本探测，得知多种提权方式
2. 使用脏牛脚本提权
# 工具和技术
>robots.txt文件泄露
>strings解插入隐写
>hackbar修改User-agent
>dirbuster网页名爆破
>crunch密码生成器
>fcrackzip zip密码爆破工具
>wpscan(wordpress cms框架扫描器)扫描后台用户名和密码爆破
>通过webshell获取www-data权限
>linpeas linux提权漏洞扫描脚本
>脏牛提权
# 渗透过程及结果
网络情况
![](tommyboy-writeup/11网络情况.png)
robots.txt文件泄露发现的flag1
![](tommyboy-writeup/12flag1.png)
首页源码提示
![](tommyboy-writeup/13首页源码提示1.png)

![](tommyboy-writeup/14首页源码提示2.png)

![](tommyboy-writeup/15首页源码提示翻译.png)

![](tommyboy-writeup/16源码提示指向链接.png)
最终找到了网站主目录
![](tommyboy-writeup/17信息搜集for图片密码1.png)
发现新的网站目录
![](tommyboy-writeup/18信息搜集for图片密码2.png)
发现flag2
![](tommyboy-writeup/19flag2.png)
发现图片，发现插入隐写
![](tommyboy-writeup/21.png)

![](tommyboy-writeup/22.png)
得到密码，解锁并查看第二篇post
![](tommyboy-writeup/23.png)

![](tommyboy-writeup/24全文.png)
发现神必ftp端口
![](tommyboy-writeup/25ftp端口.png)

爆破ftp账号

![](tommyboy-writeup/27将用户名放在字典前面.png)

![](tommyboy-writeup/28爆破成功.png)
发现ftp服务存放的提示文本
![](tommyboy-writeup/26下载readme.png)
![](tommyboy-writeup/29得到新目录.png)
发现8008网站的目录，根据提示修改UA，并爆破网页名
![](tommyboy-writeup/31访问到目录.png)

![](tommyboy-writeup/32更改useragent成功访问.png)

![](tommyboy-writeup/33网站文件爆破1.png)

![](tommyboy-writeup/34网络文件爆破2.png)
得到flag3和hint
![](tommyboy-writeup/35flag3+hint.png)
爆破backups密码
![](tommyboy-writeup/36提示文本.png)

![](tommyboy-writeup/37tommyboy上线时间.png)

![](tommyboy-writeup/38生成密码.png)

![](tommyboy-writeup/39爆破成功.png)

![](tommyboy-writeup/41解压.png)
得到提示
![](tommyboy-writeup/42提示文本.png)
wpscan扫描爆破网站后台
![](tommyboy-writeup/43通过wpscan获取用户名.png)

![](tommyboy-writeup/44爆破出密码.png)
登陆后台查看draft
![](tommyboy-writeup/45登陆后台.png)

![](tommyboy-writeup/46获得密码提示.png)
根据hint和draft提示，ssh连接靶机
![](tommyboy-writeup/47ssh登陆.png)
查看flag4
![](tommyboy-writeup/48flag4+提示文本.png)
找到flag5
![](tommyboy-writeup/49发现flag5.png)
写webshell，获得www-data权限
![](tommyboy-writeup/50制作webshell.png)

![](tommyboy-writeup/51通过webshell读取flag5.png)
提权
![](tommyboy-writeup/52通过linspea脚本探测提权漏洞.png)

![](tommyboy-writeup/53获取40616.png)

![](tommyboy-writeup/54提权.png)

![](tommyboy-writeup/55theend.png)
flag2和3
![](tommyboy-writeup/flag2.png)

![](tommyboy-writeup/flag3.png)

![](tommyboy-writeup/提示文本汇总.txt)
# 总结反思
1. dirbuster以前没用过，不过图形化什么的确实挺方便
2. linpeas这个脚本，功能十分强大
3. 脑洞不够，鬼知道stevejobs要改iphone，不过我想，可以讲所有苹果产品的UA搜集起来做成字典，用burpsuite爆破
4. 这个靶机，getroot是非必要的
5. 脏牛提权后不久，靶机就会死机，不知道其他提权方法会不会这样
# 参考资料
> 学校给的ppt
>https://www.cnblogs.com/cute/p/16331359.html linpeas脚本介绍
>https://github.com/carlospolop/PEASS-ng/tree/master/linPEAS linpeas github库
>https://www.exploit-db.com/exploits/40616 脏牛提权exp脚本