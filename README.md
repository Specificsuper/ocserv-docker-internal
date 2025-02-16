## 用途

因为安装一个 Open Connect 的步骤实在太麻烦了, 特别对于新手, 所以特意参考了 jpetazzo 的 [dockvpn](https://github.com/jpetazzo/dockvpn) 弄了一个 ocserv 的. 此项目的原因和一点点介绍可见 [使用 ocserv-docker 享受自由网络](http://wppurking.github.io/2014/10/11/use-ocserv-docker-to-enjoy-freedom-internet.html)

有啥问题可以直接 [@wyatt_pan](https://twitter.com/wyatt_pan)


## 简单部署
在 [安装好 Docker 1.0+](https://gist.github.com/wppurking/55db8651a88425e0f977) 并且正常启动 Docker 后:

* `cd ~;git clone https://github.com/Specificsuper/ocserv-docker-internal.git` : 将当前 repo 下载, 拥有可调整的 ocserv.conf 配置文件以及 ocpasswd 用户密码文件
* `docker run -d --privileged --name ocserv-docker -v ~/ocserv-docker/ocserv:/etc/ocserv -p 443:443/tcp docker-internal`  :  Box 自动下载. ocserv 的一些功能需要 Docker 在 privileged 权限下处理
* `docker logs ocserv-docker` : 查看运行日志, 检查是否正常运行(可重复执行).

```
listening (TCP) on 0.0.0.0:443...
listening (TCP) on [::]:443...
listening (UDP) on 0.0.0.0:443...
listening (UDP) on [::]:443...
```

## 构建部署
构建自己本地的 images, 计算自己的 ssl key (自签名). 可以避免 N 多人使用一个证书的问题.

* `cd ~ && git https://github.com/Specificsuper/ocserv-docker-internal.gitt` : 将当前 repo 下载, 拥有可调整的 ocserv.conf 配置文件以及 ocpasswd 用户密码文件
* `cd ~/ocserv-docker && docker build --no-cache -t docker-internal .` : 在 ocserv-docker 目录下, 重新构建全新的镜像使用. (例: 版本更新, 重新生成证书)
* `docker run -d --privileged --name ocserv-docker -v ~/ocserv-docker/ocserv:/etc/ocserv -p 443:443/tcp docker-internal`  :  ocserv 的一些功能需要 Docker 在 privileged 权限下处理
* `docker logs ocserv-docker` : 查看运行日志, 检查是否正常运行(可重复执行).

## 使用
* 初始化好的两个账户:  wyatt:616  holly:525
* 如果主服务器上开启了 iptables, 一定要记得将 443 端口的 tcp 与 udp 都开放
* 已经做了其能够处理的下发路由数量 (ocserv.conf 中, 感谢: kevinzhow 的 [route.sh](https://gist.github.com/kevinzhow/9661732) 和 [ip_cook.rb](https://gist.github.com/kevinzhow/9661753) )
* 接下来 就是 AnyConnect 的客户端了. Win, Mac, Linux [Link1](https://www.haskins.yale.edu/docdepot/published/WG/show.php?q=SEFTSzAx-58c63f59) (Cisco 官方需要注册...), [iOS](https://itunes.apple.com/us/app/cisco-anyconnect/id392790924?mt=8), [Android](https://play.google.com/store/apps/details?id=com.cisco.anyconnect.vpn.android.avf&hl=en)
* 因为我们自己生成的 CA 证书是没有权威组织认证的, 所以 AnyConnect 需要接受这些 "不信任的 VPN" :P

## 用户名
为了使新手能够最快的使用上 AnyConnect (也方便我自己同一设备能方便的链接多个不同地域的 VPS) 我预先设置了两个初始化的账号密码, 但同时将用于提供账号密码的 `ocserv/ocpasswd` 文件放在 Box 外面, 运行 Container 时使用 Volume 挂在进去, 这样方便熟悉 Docker 的用户能够方便的 使用 `ocpasswd` 命令修改或者重新生成自己的用户密码.

提供一个非常简单的更换密码操作, 复制命令就好了(建立在按照上面的操作基础上哈):
### 新添加用户
```
$> docker exec -it $(docker ps -a | grep vpn_run | awk '{print $1}') ocpasswd yourname
$> Enter password:
$> Re-enter password:
```
这个的原理是借用 docker 运行中的 container , 在其里面运行 `ocpasswd` 改变 Volumn 进去的 `./ocserv/ocpasswd` 文件内容, 所以当你运行完这行命令, 本机(非 container 中)的 `./ocserv/ocpasswd` 的文件内容会真实发生变化

### 清理掉预设的两个用户名
直接打开 `./ocserv/ocpasswd` 删掉 wyatt/holly 开头的两行就好了. 


## 信息
* Box Size: 164 MB   (原来是 380+ MB, 基础镜像缩减)
* 基础 Box: ubuntu:trusty
* 测试过的环境: 
  * [Linode 1G Ubuntu 14.04 LTS]
  * [Vultr 768MB Ubuntu 14.04 LTS]
  * [DigitalOcean 512MB Docker 1.2.0 on Ubuntu 14.04]

## Refs
* [ocserv 0.8.2 Manual](http://www.infradead.org/ocserv/manual.html)
* [[原创]linode vps debian7.5安装配置ocserv(OpenConnect server)](http://luoqkk.com/linode-vps-debian-installation-and-configuration-ocserv-openconnect-server.html)
* [Install Cisco AnyConnect Server on a Generic Linux Server](https://izhaom.in/2014/08/install-cisco-anyconnect-server-on-a-generic-linux-server/)
* [AnyConnect 带来 iPhone 上的新生活](http://imkevin.me/post/80157872840/anyconnect-iphone)
* [Install Ocserv on CentOS 6.5](https://botu.me/install-ocserv-on-centos6/)
* [Gnutls 3.1.23 on Ubuntu 14.04](http://www.bauer-power.net/2014/06/how-to-install-gnutls-3123-from-source.html)
