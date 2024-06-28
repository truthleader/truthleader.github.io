---
title: dc-2-writeup
date: 2023-12-24 14:43:02
tags:
    - 🌟
    - writeup
categories:
    - 网络安全
---

# 靶机简介
>靶机链接： https://www.vulnhub.com/entry/dc-2,311/
>靶机链接： [http://www.five86.com/dc-2.html](http://www.five86.com/dc-2.html)
>系列：- [DC](https://www.vulnhub.com/series/dc,199/)

![](dc-2-writeup/-)

# 攻击流程
## getshell
1. 绑定ip和域名
2. 通过wpscan获取用户名，并用cewl获取密码，制作成两个字典
3. msf爆破，得到两个账户
4. 通过其中一个账户，得到shell
## 提权
1. 绕过rbash
2. 尝试切换另一个账户，成功
3. sudo -l发现git的委派权限
4. git提权
# 工具和技术
>cewl通过爬虫生成字典
>rbash绕过
# 渗透过程及结果
查看网络
![](dc-2-writeup/10网络.png)
绑定域名和ip
![](dc-2-writeup/11.png)
第一个flag
![](dc-2-writeup/12flag1.png)
制作网站登录爆破字典，爆出账户密码
![](dc-2-writeup/13user.png)
![](dc-2-writeup/14password.png)
![](dc-2-writeup/15brute.png)
![](dc-2-writeup/16flag2.png)
拿shell
![](dc-2-writeup/17getshell.png)
提权
![](dc-2-writeup/18能执行的命令.png)
![](dc-2-writeup/19flag4.png)
![](dc-2-writeup/20查看权限.png)
![](dc-2-writeup/21root.png)
![](dc-2-writeup/22finalflag.png)
# 总结反思
在打DC-2的时候，在最后的提权阶段，感受到了急躁
首先就是，在得到了flag2，得到了两个用户和密码的时候，没有去试ssh，而是先去看了writeup
然后就是里面cd命令回显rbash的时候，没有首先去搜rbash，而是去看了一眼writeup
最后就是，绕过rbash之后，也没有尝试使用su进行用户切换，而是去看了一眼writeup
受不了了

1. 不要放过任何的回显
2. 尝试每一个账户，在可登录的任何地方
3. 每每感受到了障碍，就明晰进入下一步可能可以做什么？需要什么信息？当前有什么信息？可以用信息来干嘛？
# 参考资料
>https://gtfobins.github.io/gtfobins/git/#sudo 提权
>https://cloud.tencent.com/developer/article/1720937 修改环境变量中的PATH
>https://www.freebuf.com/articles/system/188989.html rbash绕过