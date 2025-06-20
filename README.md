# RKPilot
基于RK3588/香橙派的openpilot软硬件;[bilibili](https://www.bilibili.com/video/BV172VZz5EEA/?share_source=copy_web&vd_source=25cfc82384a4467c4092b69e7f853bfd)
![](./pic/comma-rk.png)
![](./pic/comma-rk0.JPG)
![](./pic/comma-rk1.JPG)
![](./pic/comma-rk2.JPG)

## 硬件图

![](./pic/connect.png)

### BOM
| 硬件 | 型号 | 备注 |
| :-----------: | :-------------:| :-------------:|
| OrangePi5 | 4G/8G版本 | [OrangePi5](http://www.orangepi.cn/html/hardWare/computerAndMicrocontrollers/details/Orange-Pi-5.html) |
| USB Cam | 1920x1080/1280x720/20~60FPS/FOV80～90 | 需要标定内参 |
| Dispaly| 800x400分辨率HDMI屏幕 | master-rk3588分支适配了这个分辨率 |
| MPU6050| common | 目前没用，后面会写驱动接进来 |
| Panda| common | 参考openpilot官方，保障安全的 |
| Relay| common | 参考openpilot官方，保障安全的 |

下面给个代码里适配了标定参数的摄像头（tb搜DF100,720p,2.8mm,无畸变,FOV90）；白天用尚可，晚上用会掉帧（<20FPS）；后期会考虑换MIPI摄像头

### Panda
https://github.com/lukasloetkolben/OpenpilotHardware/tree/main/BlackPanda

## 软件

https://github.com/MM-X/sunnypilot-pc/tree/master-rk3588

### KeyPoints
* 模型为v0.9.8版本，尽可能会持续更新。。。
* 使用的rk3588的GPU，没有使用NPU，原因是模型是fp32的，转换成rknn后精度损失太多，并且不做量化推理速度无明显优势
* 优化了原op软件USB Cam的接入速度，默认使用MJPG，帧率足够大于20HZ
* 修改了locationd，imu信息来自于can，因此较好的效果需要can有imu信息，（目前只用到yaw_rate）
* 受限于rk3588GPU（ARM Mali-G610）的推理速度（50～60ms）；对模型推理等一些进程做了降频处理：20HZ -> 10HZ；综合USB摄像头获取图像的延迟，整体端到端（曝光->控制）的延迟会比C3略高，粗略估计大概高100～200ms，勉强可接受
* 适配了部分BYD车型（来源yysnet）

## MIPI摄像头
参考[香橙派使用imx415](imx415/readme.md)

[imx415camera的tb购买地址](https://e.tb.cn/h.havxPLnMzUsQrZe?tk=bmSMVqqFa78);选的FOV90的

### KeyPoints
* 摄像头需要做参数标定，我还没来得及标，不想标可以等一等
* 需要指定`ROAD_CAM=11`
