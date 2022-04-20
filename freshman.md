# ocserv 搭建（docker版）
<!--ts-->
<!--te-->
## 1环境准备
>  准备linux 服务器一台，这里以 centos为例其他系统如ubuntu请参考[https://mirrors.tuna.tsinghua.edu.cn/help/docker-ce/](https://mirrors.tuna.tsinghua.edu.cn/help/docker-ce/)
## 2 安装docker

### CentOS/RHEL用户

如果你之前安装过 docker，请先删掉

```bash
sudo yum remove docker docker-common docker-selinux docker-engine
```

安装一些依赖

```bash
sudo yum install -y yum-utils device-mapper-persistent-data lvm2 wget
```

根据你的发行版下载repo文件

```bash
wget -O /etc/yum.repos.d/docker-ce.repo https://download.docker.com/linux/centos/docker-ce.repo
```

把软件仓库地址替换为 TUNA:

```bash
sudo sed -i 's+download.docker.com+mirrors.tuna.tsinghua.edu.cn/docker-ce+' /etc/yum.repos.d/docker-ce.repo
```

最后安装:

```bash
sudo yum makecache fast
sudo yum install docker-ce
```



## 3 拉取镜像

```bash
docker pull doourdo/ocserv 
```

##  4 启动一个容器

```bash
docker run -d --name some-ocserv -p 50443:443 doourdo/ocserv
```

## 5 账户

容器中默认提供了 3 个帐户：

| 账号  | 密码   |
| ----- | ------ |
| user1 | 123456 |
| user2 | 123456 |
| user3 | 123456 |

如果要创建新帐户，可以运行以下命令：

```bash
$ docker run it some-ocserv bash
(some-ocserv)# ocpasswd -c /etc/ocserv/ocpasswd 新用户名
Enter password:
Re-enter password:
```

## 其他容器配置请参考[https://hub.docker.com/r/doourdo/ocserv](https://hub.docker.com/r/doourdo/ocserv)

