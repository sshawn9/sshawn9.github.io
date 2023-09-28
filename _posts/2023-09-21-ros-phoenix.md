---
title: "ROS Phoenix"
categories:
  - Blog
tags:
  - ROS
  - roscore
---

## 前言
我们知道，开启roscore会启动rosmaster服务、参数服务器，以及一个rosout节点。rosmaster会跟踪各个发布者和订阅者，并为对应的发布者和订阅者建立点对点通信。这意味着，当roscore宕机时，已经建立的通信通常不会中断，但将不能保证建立新的通信连接或是新的节点注册。  

## 自动重建连接
尽管ros2已经解决了这个痛点，我仍然希望在ros1的前提下讨论一下此问题。ros中似乎也存在一些机制，如roslaunch中支持respawn选项来自动重启进程，但这个机制并不能解决roscore宕机的问题。一个更加有效的方法或许是监控roscore以及所有节点，并在必要时自动重启部分节点或服务[^1]，但这样会导致一个问题：一旦监控到roscore服务需要重启并对其重启后，其他ros节点可能也不得不重启来保证系统稳定性。  

因此，本文尝试设计一种机制，使得ros节点进程能够不依赖roscore的运行，并在roscore宕机重启后自动重建连接或服务。这样只需再有一个程序完成对roscore的监控及自动重启，即可在ros1的框架下，提升整个系统的稳定性。  

<script src="https://gist.github.com/sshawn9/c1924d0ee07a8da01879d512fd96c78e.js"></script>  

如上示例，在每次roscore重启后，程序自动重建订阅者的连接。完整的示例可以参考<a href="https://github.com/sshawn9/minimal_ros_phoenix_demo" target="_blank">这里</a>，在这个完整示例中，每当roscore停止运行时，程序会自动停止发布者/订阅者的工作。但我们知道roscore的停止通常不会影响已经建立的连接，所以也可以对程序简单修改达到在roscore停止运行时继续已经建立连接的发布者/订阅者的工作的效果，具体方法请参见代码中的注释。  

## 后记  
出于兴趣我做了这个示例并自行做了一些基本的测试，目前本工程并未正式用于任何项目中，如果使用中发现任何bug，也欢迎提出<a href="https://github.com/sshawn9/minimal_ros_phoenix_demo/issues" target="_blank">issue</a>。  

---

## Preface
We know that launching roscore will initiate the rosmaster service, parameter server, and a rosout node. Rosmaster tracks various publishers and subscribers, and establishes point-to-point communication for corresponding publishers and subscribers. This implies that, when roscore crashes, the already established communication usually will not be interrupted, but it cannot guarantee the establishment of new communication connections or new node registrations.  

## Automatic Resume  
Although ros2 has addressed this pain point, I still wish to discuss this issue under the premise of ros1. There seems to be some mechanisms in ROS, such as the respawn option in roslaunch to automatically restart processes[^1], but this mechanism cannot solve the problem of roscore crashing. A more effective method might be to monitor roscore and all nodes, and automatically restart certain nodes or services when necessary, but this leads to a problem: once it is monitored that the roscore service needs to be restarted and is restarted, other ros nodes may also have to be restarted to ensure system stability.

Therefore, I attempt to design a mechanism that allows ROS node processes to operate independently of roscore, and automatically reestablish connections or services after roscore restarts. In this way, only one more program is needed to monitor and automatically restart roscore, which can enhance the stability of the entire system within the framework of ros1.

<script src="https://gist.github.com/sshawn9/c1924d0ee07a8da01879d512fd96c78e.js"></script>  

As shown in the example above, the program automatically rebuilds the subscriber's connection every time roscore restarts. The complete example can be referred to <a href="https://github.com/sshawn9/minimal_ros_phoenix_demo" target="_blank">here</a>, in which, whenever roscore stops running, the program will automatically stop the publisher/subscriber's work. However, we know that the stopping of roscore usually does not affect the already established connections, so the program can also be simply modified to achieve the effect of continuing the work of the already connected publishers/subscribers when roscore stops running. For specific methods, please refer to the comments in the code.

## Epilogue  
Out of interest, I created this example and conducted some basic tests myself. Currently, this project has not been formally used in any others. If any bugs are found during use, you are welcome to raise an <a href="https://github.com/sshawn9/minimal_ros_phoenix_demo/issues" target="_blank">issue</a>.


[^1]: This reminds me of some concepts in Kubernetes.  
