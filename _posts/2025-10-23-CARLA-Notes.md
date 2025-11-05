---
title: "CARLA Notes"
categories:
  - Note
---

一些关于CARLA的笔记。

部分信息需要核实，或部分内容仅为助记，或者版本更迭后有所变化。


#### Coordinate Systems

|                  | E   | N      | W         | S         | Convert    |            |
| ---------------- | --- | ------ | --------- | --------- | ---------- | ---------- |
| my definition(A) | 0   | 90     | 180(-180) | -90       |            | right hand |
| carla compass(B) | 90  | 0(360) | 270       | 180       | 90 - B = A | left hand  |
| carla yaw(C)     | 0   | -90    | -180(180) | 90        | -C = A     | left hand  |
| legacy(D)        | -90 | 0      | 90        | -180(180) | D + 90 = A | right hand |
| carla transform  | +X  | -Y     | -X        | +Y        |            |            |

#### Center of Mass

https://github.com/carla-simulator/carla/issues/4487

#### Wheel Index Definition

https://carla.readthedocs.io/en/latest/python_api/#carlavehiclephysicscontrol

#### About Steer

在CARLA中，定义左转转角为负值，右转转角为正值。

同时在[车辆控制接口](https://carla.readthedocs.io/en/latest/python_api/#carla.VehicleControl)中，steer的范围是[-1.0, 1.0]，而对于具体的车辆，其存在一个属性为[max_steer_angle](https://carla.readthedocs.io/en/latest/python_api/#carla.WheelPhysicsControl)表示该车辆最大的前轮转角，对于[vehicle.tesla.model3](https://carla.readthedocs.io/en/latest/bp_library/)是70度，也就是说如果你通过接口发送steer的量为0.5，那么车辆会执行0.5 * 70 = 35度的前轮转角，不过由于转向梯形的存在，实际上这个时候是右前轮右转35度，而左前轮会右转一个小于35度的角度。反之亦然，如果发送-0.5，那么就是左轮左转35度，右轮左转一个小于35度的角度。

那么自动驾驶时呢，就需要标定前轮转角和实际曲率的关系，这里以vehicle.tesla.model3为实验车辆。

| steer | 左前轮（左转为正） | 右前轮（左转为正） | 实际路径曲率（左转为正） |
| ----- | --------------- | --------------- | -------------------- |
| -0.4  | 28°             | 22.3229°        | 0.1462               |
| -0.3  | 21°             | 17.5611°        | 0.1121               |
| -0.2  | 14°             | 12.3545°        | 0.0758               |
| -0.1  | 7°              | 6.5576°         | 0.0392               |
| -0.05 | 3.5°            | 3.3854°         | 0.01975              |
| -0.02 | 1.4°            | 1.3813°         | 0.0080               |
| -0.01 | 0.7°            | 0.6953°         | 0.0040               | 

| 名义控制量（-steer）/u | 实际路径曲率/k |
| -------------------  | ------------ |
| 0.4                  | 0.1462       |
| 0.3                  | 0.1121       |
| 0.2                  | 0.0758       |
| 0.1                  | 0.0392       |
| 0.05                 | 0.01975      |
| 0.02                 | 0.0080       |
| 0.01                 | 0.0040       | 

注意到ackermann运动学关系为：  
$$k = \frac{1}{R} = \frac{\tan(\delta)}{L}$$  

而 $\tan(\delta)$ 可以被泰勒级数展开：  
$$\tan(\delta) = \delta + \frac{1}{3}\delta^3 + \frac{2}{15}\delta^5 + \dots$$  

这里就用三次函数来进行拟合，拟合结果是：

$$k = 0.045407365999453636 * u^3 + -0.1032605725745423 * u^2 + 0.3997019204703261 * u$$
$$u = -0.748291607126577 * k^3 + 1.718887821431124 * k^2 + 2.499327137790437 * k$$


这里顺带聊一下自动驾驶转向标定好了，这个话题其是可以拿出来单写一篇博客了，这里就随性写一下吧。

一般来说，这个工作主要涉及三个状态量，方向盘转角、前轮转角和实际转向曲率，而我们的目的就是通过控制某个转角来使车辆达到我们的期望曲率，或者通过某个转角得到当前的状态曲率。

系统性的聊这个事还挺难的，现在夜里一点了，有点困了，列几个点好了。

1. Ackermann转向模型
    - 实际转向梯形通常不会完全符合该模型
    - 速度达到一定程度时，轮胎动力学的影响也会有所体现
2. 方向盘转角与前轮转角之间的关系
    - 一般来说，应考虑他们之间存在非线性关系
    - 方向盘转角零位附近，可能存在“死区”，或者更一般的说，考虑实际的转向机构，在任何转角下，如果方向盘转动的方向改变，由于机械公差/间隙的存在，会导致方向盘产生自由行程。针对不同转向机构，这个现象的表现也会不一样，具体现象具体分析。
    - 方向盘零位可能存在漂移或偏差，致使方向盘转角0不对应曲率0.
    - 转向系统可能存在非对称性，比如左转和右转的对应关系存在差异，不符合理论上的奇函数性质。

实际上，很多细节问题大部分人可能都遇不到，你会发现直接用理论阿克曼模型也没什么问题，一来你对跟踪精度的要求可能没那么高，二来运动控制算法周期性的闭环调整不断弥补理论模型和实际响应的偏差，使其看起来也还凑合。也有可能木桶的短板还没有到这里。

聊点题外话，从转向标定到横向控制，我遇到一些搞“学术”的同学做的东西落不了地，一些做工程的同学解决问题又找不到门路。学术、工程，好像这是两个门派似的。理论要和实际结合的嘛！

比方说当一个方向盘转角和前轮转角之间的比例关系非线性时，我标定了一个固定比例系数然后开始测试我的“伟大”算法，在自动驾驶时表现出明显的转向不足或转向过度，我来一句：理论和实际存在差异。这合适吗？这对吗？对理论的理解浅显至此。

不说这个了，现实情况可能比我说的离谱。

继续CARLA中转角标定，可以看到我做的其实很随意（不是不认真哈，我很严谨的），只做了单边，假定不存在零位偏差，简单用一个三次多项式拟合。这是因为在仿真世界中我可以猜测某些预设是大概率满足的，一旦这个标定结果在控制算法中应用表现不符合预期，我会重新探究。

实车的转向标定理论没什么区别，操作可能有些不同。还是那句：实事求是。


#### Other

https://carla-assets.s3.us-east-005.backblazeb2.com/20221201_5ec9328.tar.gz  

https://leaderboard.carla.org/  

https://eval.ai/web/challenges/challenge-page/2098/overview  

https://eval.ai/web/challenges/challenge-page/1856  

https://www.nuscenes.org/nuplan  



- carla配置文件
  修改配置文件来永久改变渲染设置，路径一般在CARLA安装目录的`Config`文件夹下，例如：
  ```ini
  [SystemSettings]
  r.ViewDistanceScale=0.1
  r.SkeletalMeshLODBias=5
  r.ShadowQuality=0
  r.LightFunctionQuality=0
  r.TextureQuality=0
  r.FilterQuality=0
  r.Streaming.MipBias=4
  r.MaxAnisotropy=0
  r.Streaming.LimitPoolSizeToVRAM=1
  r.Streaming.PoolSize=200
  r.Foliage.DensityScale=0
  r.LandscapeLODBias=3
  r.MotionBlurQuality=0
  r.AmbientOcclusionLevels=0
  r.AmbientOcclusionRadiusScale=0.1
  r.DepthOfFieldQuality=0
  r.RenderTargetPoolMin=400
  r.LensFlareQuality=0
  r.SceneColorFringeQuality=0
  r.EyeAdaptationQuality=0
  r.BloomQuality=0
  ```

- 关闭无关地图细节  
  有时候，简化地图或使用特定设计的低复杂度地图也可以显著提高性能：
  ```bash
  # 使用一个基础的测试地图
  ./CarlaUE4.sh /Game/Maps/Minimal
  ```
