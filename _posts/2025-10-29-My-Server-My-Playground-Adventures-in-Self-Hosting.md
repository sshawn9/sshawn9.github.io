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
