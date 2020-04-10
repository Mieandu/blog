# Hyper-V基础图形界面（Basic Graphics Mode）安装Centos7

## 背景

由于公司配的电脑屏幕比较小、分辨率低，在Hyper-V上安装Centos7时碰到界面显示不完整，无法进行下一步的尴尬局面，在网上找了一圈也没找到合适的解决方案。但是发现可以用basic graphics mode方式进行安装，在网上也没找到这钟安装方式的资料，特记录下来，希望能给大家带来帮助。

## 开始安装

Basic Graphics Mode 在 Troubleshooting 里面，如图。

![基础图形界面](file://C:/Users/2271/Pictures/basic%20graphics%20mode.png)
回车以后会进入如下界面：
![基础图形界面2](file:///C:/Users/2271/Pictures/2.png)

会看到很多选项，这里说一下各个选项中元素的意思，了解每个元素的意思就好办了

每个选项分成3部分： [选项] [状态] [选项名称]

- 选项即前面的数字
- 状态分3个： `空`-可以不管 `X`-可以不用配置 `！`-警号表示需要配置

> ps : 我们的目标是把`！`号标识的选项变成`X`，这样按`b` 回车就可以开始安装了，不然按 `b`是不会开始安装的。

选择1，配置时区
![基础图形界面3](file:///C:/Users/2271/Pictures/3.png)

选择2（Asia）亚洲
![基础图形界面](file:///C:/Users/2271/Pictures/4.png)

选择65（Shanghai）上海
![基础图形界面3](file:///C:/Users/2271/Pictures/5.png)

可以看到Time settings 变成 `[X]` 了
![基础图形界面](file:///C:/Users/2271/Pictures/6.png)

Network configuration 和Root password没有配，最后的 User creation可以不用配的
![基础图形界面](file:///C:/Users/2271/Pictures/7.png)

选择2，配置网卡
![基础图形界面](file:///C:/Users/2271/Pictures/8.png)

配置网卡，7和8都选好，进入系统之后就不用再额外配置网络了，从图形化界面安装，进入系统是无法上网的，需要配置一下网卡

![基础图形界面](file:///C:/Users/2271/Pictures/9.png)

录入root的密码，录好就可以开始安装了
![基础图形界面](file:///C:/Users/2271/Pictures/10.png)

因为我密码设置的是123456的弱密码，所以给了提示，输入'yes'就行了
![基础图形界面](file:///C:/Users/2271/Pictures/11.png)

其它的如Installation Destination(安装源)和Installation Destination(安装路径)按照默认，一直按`c`(continue)就行，User Creation（创建用户）可以根据需要以后再配，我们有root用户就行

> ps : 配置完了，有的选项可能会重新变回`!`，这时候进去，一直按`c`(continue)就可以了

都配置完了按下`b`(begin)可以看到已经开始安装了
![基础图形界面](file:///C:/Users/2271/Pictures/15.png)

安装完成就可以看到熟悉的登录界面了，大功告成
![基础图形界面](file:///C:/Users/2271/Pictures/end.png)

过程其实还是很简单的，跟图形界面要配置的地方基本一致，希望大家都能够顺利完成安装。
