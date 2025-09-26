# 📘 教程：奥比中光 Gemini 2 深度相机适配 LeRobot 框架（SOARM 系列）

> 本教程将带你完成 **Orbbec Gemini 2 深度相机** 与 **LeRobot 框架** 的适配，帮助你在 SOARM 系列机械臂控制与视觉任务中获取稳定的 RGB-D 数据流。  
> 🎯 **目标**：实现相机数据采集，并作为 LeRobot 的输入源。

---

## ✨ 前置条件

### 🔧 硬件
- 奥比中光 **Gemini 2** 深度相机  
- USB 3.0 接口  
- Type-C 接口  
- 主机（Ubuntu 22.04）

### 💻 软件依赖
- Ubuntu 22.04  
- Python 3.10  
- 已安装 **LeRobot 所需环境**（参照 [Seeed 官方 Wiki](https://wiki.seeedstudio.com/cn/lerobot_so100m_new/)）  

⚠️ **以下所有操作均基于上述环境完成**

---

## 🚀 步骤 1：安装 Orbbec SDK 依赖环境

1. 拉取 `pyorbbec` 仓库
   ```bash
   git clone https://github.com/orbbec/pyorbbecsdk.git
   ```

2. 下载并安装 SDK 对应的 **.whl 文件**  
   前往 [pyorbbecsdk Releases](https://github.com/orbbec/pyorbbecsdk/releases)，  
   根据 Python 版本选择并安装，例如：
   ```bash
   pip install pyorbbecsdk-x.x.x-cp310-cp310-linux_x86_64.whl
   ```

3. 在 `pyorbbec` 目录下安装依赖
   ```bash
   cd pyorbbecsdk
   pip install -r requirements.txt
   ```

---

## 🚀 步骤 2：相机校准与调试

请依照 [Seeed 官方 Wiki](https://wiki.seeedstudio.com/cn/lerobot_so100m_new/)  
进行 **相机校准与调试**，确保 RGB-D 数据流能够正确获取。

---

## 🚀 步骤 3：函数调用与示例

以下示例均需将 `so10x` 替换为实际型号（如 `so100` / `so101`）。

### 🔹 3.1 相机接入与遥操控

我们加入了focus_area超参数，因为过远的深度数据对于机械臂没有意义（抓取不到），因此小于或者大于focus_area的深度数据将会变为黑色,默认的focus_area是(20,600)
目前支持的分辨率只限于 width: 640, height: 880

```bash
python -m lerobot.teleoperate \
  --robot.type=so10x_follower \
  --robot.port=/dev/ttyACM1 \
  --robot.id=my_awesome_follower_arm \
  --robot.cameras="{ front: {type: orbbec, width: 640, height: 880, fps: 30, focus_area:(20,600)}}" \
  --teleop.type=so10x_leader \
  --teleop.port=/dev/ttyACM0 \
  --teleop.id=my_awesome_leader_arm \
  --display_data=true
```

### 🔹 3.2 数据采集
```bash
python -m lerobot.record \
  --robot.type=so10x_follower \
  --robot.port=/dev/ttyACM1 \
  --robot.id=my_awesome_follower_arm \
  --robot.cameras="{ front: {type: orbbec, width: 640, height: 880, fps: 30, focus_area:(20,600)}}" \
  --teleop.type=so10x_leader \
  --teleop.port=/dev/ttyACM0 \
  --teleop.id=my_awesome_leader_arm \
  --display_data=true \
  --dataset.repo_id=seeedstudio123/test \
  --dataset.num_episodes=5 \
  --dataset.single_task="Grab the black cube" \
  --dataset.push_to_hub=false \
  --dataset.episode_time_s=30 \
  --dataset.reset_time_s=30
```

### 🔹 3.3 模型训练
无需修改，保持原有训练流程。

### 🔹 3.4 模型评估
```bash
python -m lerobot.record \
  --robot.type=so10x_follower \
  --robot.port=/dev/ttyACM1 \
  --robot.cameras="{ front: {type: orbbec, width: 640, height: 880, fps: 30}}" \
  --robot.id=my_awesome_follower_arm \
  --display_data=false \
  --dataset.repo_id=seeed/eval_test123 \
  --dataset.single_task="Put lego brick into the transparent box" \
  --policy.path=outputs/train/act_so101_test/checkpoints/last/pretrained_model
```
###  3.5 requirment.txt环境配置
在requirment.txt写如下配置
```requirment.txt
pybind11==2.11.0
pybind11-global==2.11.0
opencv-python
wheel
numpy<2.0  # see https://github.com/orbbec/pyorbbecsdk/issues/47
av # for h264 decoding
pygame # for visualization
pynput # for keyboard input
https://github.com/orbbec/pyorbbecsdk/releases/download/v2.0.13/pyorbbecsdk-2.0.13-cp310-cp310-linux_x86_64.whl

```
最后一行是pysdk，直接从github拉取，经我们测试可以做到不用自己编译，但是环境的numpy版本仍然要小于2.0,如果强行升numpy版本，将需要重新编译才能正常工作，这主要是因为pybind版本导致的，目前已经在和Orbbec官方沟通
---

## 🔍 注意事项

- `--robot.cameras` 参数中的 `type: orbbec` 即为接入 Gemini 2 的关键。  
- `--dataset.repo_id` 建议替换为你自己的 **Hugging Face Hub 项目 ID**，以便数据管理。

---

## 📖 参考资料

- [LeRobot 官方 GitHub](https://github.com/huggingface/lerobot)  
- [Seeed Studio LeRobot Wiki](https://wiki.seeedstudio.com/cn/lerobot_so100m_new/)  
- [Orbbec 开发者中心](https://developer.orbbec.com.cn/)  
- [pyorbbecsdk GitHub](https://github.com/orbbec/pyorbbecsdk)

---

## 💡 作者与贡献

- 作者: 张家铨，王文钊  
- 欢迎提交改进建议与 PR！
