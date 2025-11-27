---
title: "My Server, My Playground: Adventures in Self-Hosting"
categories:
  - Blog
---

隐约记得我最开始想要一个服务器的原因是我想利用服务器来远程开发，因为当时我非常喜欢MBP的屏幕素质，但是我的开发工作需要Linux环境，俺的MBP还是单热管压i9那一代，散热太拉了，不管是开容器还是跑虚拟机都达不到直接使用Ubuntu物理机的流畅体验，那个时候我就想要是有个高性能服务器就好了。

好了前面都是废话，高性能服务器好贵我舍不得，基本上都是用不超过4c4g的配置，不过也很有用了。

### [Nextcloud](https://nextcloud.com/)  
```bash
sudo snap install nextcloud
```

或者使用docker安装：

```bash
docker run -d -p 8080:80 -v ./nextcloud:/var/www/html --restart always --name nextcloud nextcloud
```

用法不介绍了，很简单。

### [Reactive Resume](https://rxresu.me/)
有很多在线制作简历的网站，包括一些我觉得做的很好看很棒的，但是简历是包含不少隐私信息的，所以我找到了这个工具，可以自托管，功能算不上完美，不过也够用了。

[Self-Hosting using Docker](https://docs.rxresu.me/product-guides/self-hosting-reactive-resume-using-docker)  

注意把教程提供的`docker-compose.yml`文件中的URLs按需修改：
```yaml
      # -- URLs --
      PUBLIC_URL: http://localhost:3000
      STORAGE_URL: http://localhost:9000/default
```

### [Hosting Docs with Read the Docs Sphinx Theme](https://sphinx-rtd-theme.readthedocs.io/en/stable/)  

这种风格的文档相信大家已经见过很多了，包括ros2的文档也使用这个工具。

我也使用该框架来写一些文档，并搭配[nginx](https://nginx.org/)反向代理来托管到服务器上。

不过后面我大概会把托管的文档撤掉，这里就不放链接了，放张截图看看效果吧。
![](/assets/images/autonomous-documentation-with-readthedocs.png)

### Getting Started with Cloudflare

(Updated: Nov 2025)

最近我买了了香港服务器，8c16g，使用体验的话只能说凑合，主要是ssh连接时常会有一种卡顿感，毕竟价格也很凑合。

随后我又在Cloudflare购买了一个域名，随后进行了一系列操作，不成体系，仅作大概记录以备不时之需。

对了，[caddy](https://caddyserver.com/)真好用。

后面我大概还会部署下面这些服务：
- jupyter notebook
- [openvscode-server](https://github.com/gitpod-io/openvscode-server)
- 一个在线笔记/备忘录应用，追求轻量方便且快速
- gogs

还有一个需要注意的点，服务器上装完docker启用[containerd-snapshotter](https://docs.docker.com/engine/storage/containerd/#enable-containerd-image-store-on-docker-engine)，我需要Multi-platform builds依赖这个玩意，这个功能开关状态对应的image和container是隔离的。

此外我还了解了一些关于网络安全的知识，比如服务只允许通过cloudflare的ip流量，容器只允许localhost访问其端口服务，再通过反向代理到域名之类的。

#### 个人主页部署到域名网站

有了域名自然就想到让我的github pages个人博客网站可以直接通过域名访问，但是目前我还不打算直接ban掉github pages，所以最初我是通过github actions再编译一份静态资源文件并用rsync同步到服务器，然后用caddy反向代理到指定域名，域名解析在cloudflare中已经设置过。

不过cloudflare提供了pages，和github pages功能一样，但是大陆可以访问，虽说由于众所周知的原因访问质量很一般，cloudflare也提供了付费的Argo Smart Routing提升访问质量。我想既然部署在我的服务器上流量也要过cloudflare，那干脆直接用cloudflare pages好了。

其中cloudflare部署jekyll/minimal-mistakes的网站时设置如下环境变量：

![](/assets/images/cloudflare_pages_jekyll_environments.png)

不过主观感觉速度好像还不如部署在我的服务器然后让cloudflare负责域名解析。

#### [1Panel](https://github.com/1Panel-dev/1Panel)

手机端居然要专业版才可以用，可恶。

多聊几句吧，现在NAS不是也挺火的吗，一些玩家没接触太多linux，可以通过类似的面板应用快速部署容器应用，不过对于我来说，这个玩意目前来看很鸡肋了，也就是看看面板图一乐。

还有个宝塔面板似乎也不少人用，个人没啥感觉。

#### [Open WebUI](https://github.com/open-webui/open-webui)/[One API](https://github.com/songquanpeng/one-api)

简单来说，这就是一个通过向服务商购买API Key额度然后自建web前端使用大语言模型的方案。

我把Gemini API/Azure Openai/Azure AI Foundry都搞了一遍，折腾一天，效果一般，体验不如老老实实继续每个月付20刀，主要我也不想在这上面投入时间继续深入研究。

#### Cloudflare Tunnel

这个我还没做，以后有时间试试，理论上借助这个可以把闲置笔记本当服务器用了。

#### 远程桌面

跑题一下，写的时候想到这个了，记录一下。

各种各样的远程桌面方案总是有各种各样的缺点或者不稳定，比如microsoft remote desktop我遇到过好多次断连需要重启客户端/服务端才能重新连接的情况。

rustdesk浅浅尝试了下，也没有惊艳我，持续关注吧。

其他一些远程桌面不值一提了。

[贝锐](https://www.oray.com/)有一些软硬件一体化方案，侵入性有点高，但也可以关注，只要足够好也可以。

#### EasyTier

```service
# cat /etc/systemd/system/easytierweb.service 
[Unit]
Description=EasyTier Web Service
After=network.target syslog.target
Wants=network.target

[Service]
Type=simple
ExecStart=/opt/easytier/easytier-web-embed --api-host http://your-server-ip:11211

[Install]
WantedBy=multi-user.target
```

```bash
./easytier-cli service install -w udp://your-server-ip:22020/admin --machine-id some-id --hostname some-name
```
