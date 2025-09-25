# 📘 教程：奥比中光 Gemini 2 深度相机适配 LeRobot 框架 （SOARM系列）

> 本教程将带你完成 **Orbbec Gemini 2 深度相机** 与 **LeRobot 框架** 的适配，帮助你在机器人控制与视觉任务中获取稳定的 RGB-D 数据流。  
> 目标：实现相机数据采集，并作为 LeRobot 的输入源。

---

## ✨ 前置条件

### 硬件
- 奥比中光 **Gemini 2** 深度相机  
- USB 3.0 接口  
- 主机（推荐 Ubuntu 20.04 / 22.04）

### 软件依赖
- Ubuntu 20.04+ / Windows 11（推荐 Ubuntu）  
- Python 3.9+  
- CMake 3.16+  
- 已安装 [LeRobot 框架](https://lerobot.org/)  
- 已安装奥比中光 **Orbbec SDK 2.0**（含驱动）

---

## 🚀 步骤 1：安装 Orbbec SDK

1. 下载 SDK  
   前往 [Orbbec 官方下载页](https://developer.orbbec.com.cn/) 获取 Gemini 2 对应的 SDK（Linux x86_64 版本）。  

2. 解压并安装
   ```bash
   tar -zxvf OrbbecSDK_x.x.x_Ubuntu20_x86_64.tar.gz
   cd OrbbecSDK_x.x.x_Ubuntu20_x86_64
   sudo ./install.sh
