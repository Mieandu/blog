# Kubernetes安装

## 环境

CentOS Linux 7
Docker 18.09

## 配置

CentOS Linux 7默认启动了防火墙服务（firewalld），而Kubernetes的Master与工作Node之间会有大量的网络通信，安全的做法是在防火墙上配置各组件需要相互通信的端口号，在网络安全的情况下可禁用防火墙。

`systemctl disable firewalld`

建议在主机上禁用SELinux，让容器可以读取主机文件系统
`setenforce 0`
或修改系统文件/etc/sysconfig/selinux，将SELINUX=enforcing修改成SELINUX=disabled，然后重启Linux。

> ps: 安装docker 可以使用网上教程进行安装(CentOS 7 安装Docker)[https://blog.csdn.net/u014069688/article/details/100532774]

## 生产环境需要额外关注

- 安全 CA、token、basic authentic
- 跨主机通信 FLANNEL...工具的选择
- 私有化Docker镜像仓库
- Kubelet 设置pause镜像
- Kubernetes升级
