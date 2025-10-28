---
title: "Ubuntu Software Updates"
categories:
  - Note
---

当我们更新软件时，偶尔会遇到不能顺畅更新的情况，通常这和我们所处的网络环境有关，在这里记录一些情况和解决方法。

### VPN
咱这个行业这属于必修课了，当然我们的目的只是为了获得必要的开发资源。

##### Clash
一款极其知名的网络代理核心。

- [Clash for Windows](https://github.com/lantongxue/clash_for_windows_pkg/releases/tag/0.20.39)  
  尽管该客户端已经不再更新，但它依然是目前我最优先的选择。

- [Clash Verge Rev](https://www.clashverge.dev/)  
  一款目前活跃更新中的客户端，Clash Verge的延续，使用了目前我十分感兴趣的Rust/Tauri技术栈，我期待它的未来，不过目前我还不打算切换至该客户端。

### apt

##### aptitude
```bash
sudo apt install aptitude
```
神器！都遇到过Ubuntu依赖出现问题的情况吧？aptitude大多数情况下可以给出解决方案并让你选择是否使用，必须在用。

##### force IPv4

某些网络环境下，IPv6的支持并不完善，使用IPv4可以顺畅更新。

```bash
sudo echo 'Acquire::ForceIPv4 "true";' > /etc/apt/apt.conf.d/99force-ipv4
```

##### [chsrc](https://chsrc.run/)
全平台通用换源工具与框架，非常好用，也不完美，比如某些源更新不及时，或是收录了不可靠的源，主要是ros2，不知道是否有修复，但总体很棒，在用。

##### [https://linuxmirrors.cn/](https://linuxmirrors.cn/)
目前没有在用。

### ros2 source with ubuntu
ros2的安装教程中，早期是使用手动添加source list条目和GPG密钥的方式来安装ros2的apt源，现在已经更改为了从该[地址](https://github.com/ros-infrastructure/ros-apt-source/releases)下载一个特定的ros2-apt-source.deb并安装既可。

```bash
➜  Downloads dpkg -c ros2-apt-source_1.1.0.noble_all.deb 
drwxr-xr-x root/root         0 2025-05-21 23:16 ./
drwxr-xr-x root/root         0 2025-05-21 23:16 ./etc/
drwxr-xr-x root/root         0 2025-05-21 23:16 ./etc/apt/
drwxr-xr-x root/root         0 2025-05-21 23:16 ./etc/apt/sources.list.d/
drwxr-xr-x root/root         0 2025-05-21 23:16 ./usr/
drwxr-xr-x root/root         0 2025-05-21 23:16 ./usr/share/
drwxr-xr-x root/root         0 2025-05-21 23:16 ./usr/share/doc/
drwxr-xr-x root/root         0 2025-05-21 23:16 ./usr/share/doc/ros2-apt-source/
-rw-r--r-- root/root       242 2025-05-21 23:16 ./usr/share/doc/ros2-apt-source/changelog.gz
-rw-r--r-- root/root       452 2020-04-16 20:00 ./usr/share/doc/ros2-apt-source/copyright
drwxr-xr-x root/root         0 2025-05-21 23:16 ./usr/share/keyrings/
-rw-r--r-- root/root      1167 2020-04-16 20:00 ./usr/share/keyrings/ros2-archive-keyring.gpg
drwxr-xr-x root/root         0 2025-05-21 23:16 ./usr/share/ros-apt-source/
-rw-r--r-- root/root      1793 2025-05-21 23:16 ./usr/share/ros-apt-source/ros2.sources
lrwxrwxrwx root/root         0 2025-05-21 23:16 ./etc/apt/sources.list.d/ros2.sources -> /usr/share/ros-apt-source/ros2.sources
➜  Downloads 
```

显然其中的`./usr/share/ros-apt-source/ros2.sources`就是ros2的源相关文件，让我们查看其中的内容：

```bash
➜  Downloads ar p ros2-apt-source_1.1.0.noble_all.deb data.tar.zst | tar --zstd -Ox ./usr/share/ros-apt-source/ros2.sources 
Types: deb deb-src
URIs: http://packages.ros.org/ros2/ubuntu
Suites: noble
Components: main
Signed-By:  -----BEGIN PGP PUBLIC KEY BLOCK-----
 .
 mQINBFzvJpYBEADY8l1YvO7iYW5gUESyzsTGnMvVUmlV3XarBaJz9bGRmgPXh7jc
 VFrQhE0L/HV7LOfoLI9H2GWYyHBqN5ERBlcA8XxG3ZvX7t9nAZPQT2Xxe3GT3tro
 u5oCR+SyHN9xPnUwDuqUSvJ2eqMYb9B/Hph3OmtjG30jSNq9kOF5bBTk1hOTGPH4
 K/AY0jzT6OpHfXU6ytlFsI47ZKsnTUhipGsKucQ1CXlyirndZ3V3k70YaooZ55rG
 aIoAWlx2H0J7sAHmqS29N9jV9mo135d+d+TdLBXI0PXtiHzE9IPaX+ctdSUrPnp+
 TwR99lxglpIG6hLuvOMAaxiqFBB/Jf3XJ8OBakfS6nHrWH2WqQxRbiITl0irkQoz
 pwNEF2Bv0+Jvs1UFEdVGz5a8xexQHst/RmKrtHLct3iOCvBNqoAQRbvWvBhPjO/p
 V5cYeUljZ5wpHyFkaEViClaVWqa6PIsyLqmyjsruPCWlURLsQoQxABcL8bwxX7UT
 hM6CtH6tGlYZ85RIzRifIm2oudzV5l+8oRgFr9yVcwyOFT6JCioqkwldW52P1pk/
 /SnuexC6LYqqDuHUs5NnokzzpfS6QaWfTY5P5tz4KHJfsjDIktly3mKVfY0fSPVV
 okdGpcUzvz2hq1fqjxB6MlB/1vtk0bImfcsoxBmF7H+4E9ZN1sX/tSb0KQARAQAB
 tCZPcGVuIFJvYm90aWNzIDxpbmZvQG9zcmZvdW5kYXRpb24ub3JnPokCVAQTAQgA
 PgIbAwULCQgHAgYVCgkICwIEFgIDAQIeAQIXgBYhBMHPbjHmut6IaLFytPQu1vur
 F8ZUBQJoEhoGBQkUtHZwAAoJEPQu1vurF8ZUv1AP/2gID+uw7pw3WpPevny3pliZ
 JeDx4Y+ut+5c2nCfkpUc3lG50v9ly4ZpNQTWKIm9yB6dxgary7EKpAlGVmiU75JA
 LyftVtjeyQcre2f7Z00u2lXw8Red52AsWHkh/dtctgLSGQiJdTd0donO6cszZFVa
 sCiFdRKlizGvBkE8uFdKYMGixOgnvQZrb9OLqRsoj10aDzN0X3NJk1LTxiS3+udY
 poOk2Bm9VGyrNmgIrYiNqbYPBHYkWGHBqJxvAK92lJ2I/n6X4U8r6sMdDE7QDw4j
 FMdrxC0XmCL4cFPkkR1qadtJy9FiCtpKyqiKuUsCG6AUi5EOY+7Y3oSpKn8Wp1K5
 VMbv12JRIatDIeaAnwa2qyBQVAVC1F/OqWUFKluPjKyMR3DXKwjxpt1P+HUmda0w
 HcnhFIu2th/egmGKH5e3atcVxjAxYfm+f92MN7fFEuFQsMZhI/gt3IgESWrgdaAz
 opRInrMz7yEtz3VaaehwmUUR2gevPQMzBRaA+NIqMLDUvV5jujvFe8c1VUtBLTYc
 /alBiM/Mo1niy3aUfDahzhTr6zz+ur6BFRnNFWv56M3NOVlreNm3NIbNX2kTKh0Z
 QJSSCklJuDUqnPmAzT2BZWUpwfe7QYRwvQhF0YB2N1LavyNwiyfinCQlAh+Q9eme
 2jqGsxvQym3sAPnWvA68
 =xH9H
 -----END PGP PUBLIC KEY BLOCK-----
➜  Downloads 
```

显然，<http://packages.ros.org/ros2/ubuntu>就是ros2的apt源地址，并且其安装位置为`/usr/share/ros-apt-source/ros2.sources`.

接下来就很简单了，该deb安装完成后，将上面文件中的链接相应的修改为<https://mirrors.aliyun.com/ros2/ubuntu/>就可以换成阿里的ros2源了。

多提一句，我测试了多个ros2的镜像源，大多数国内ros2镜像源都是内容没有完全同步不可用的，唯独阿里的ros2镜像源是可用的，这里给阿里云点赞，顺带一体我在用阿里云服务器，感觉也很不错。

以上过程没什么技术难度，但很可惜我遇到一些人就是不会去看这些细节，只知对着教程一步一步机械性操作，不懂探究教程的每一步背后的原理，或许这也是技术软实力一个维度的体现吧。

##### thirdparty tools

我注意到一些人使用[fishros](https://fishros.com/)提供的一键安装脚本，早期其解决了一些rosdep在国内网络环境下遭遇的困难。

首先，我并不会一味的吹毛求疵只当一个批评者，我认可该工具的价值，如果你是该领域的小白并且你并不需要在该领域精进，那么我完全支持你使用该工具。

但如果你是和我一样的专业开发人员，应当有对工具的判断力，此外，你的水平应该不需要依赖于该工具，当然合理利用其提供的某些资源是可以的，总感觉该观点会被喷。简而言之，该工具挺好的，其完成度也不错，商业化运作不了解，但似乎也不错，但对于专业开发人员来说，目前不足以替代官方途径。

**PS:** 我实际测试使用了该工具并得出其不适合专业开发人员的结论，不过其缺点我已经忘记了，好久之前的事了。


### Others

##### [proxychains-ng](https://github.com/rofl0r/proxychains-ng)

很棒的一个工具，可以让终端下的命令走代理，但并不完美。现在我已经几乎不使用它了，Clash的TUN Mode更好用。

这个属于代理工具了，但也算是和本文主题相关。

##### terminal proxy by setting environment variables
emmmmmm，也相关？算了我就想写在这，反正这是一篇Note不是Blog。目前没有在用这个。
```bash
alias setproxy="export ALL_PROXY=socks5://127.0.0.1:1080"
alias unsetproxy="unset ALL_PROXY"
```
