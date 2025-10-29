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
