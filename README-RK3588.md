# RK3588平台构建指南 / RK3588 Build Guide

[English Version Below](#english-version)

## 中文版本

### 概述

本文档详细说明如何在基于RK3588处理器的设备上构建和运行dx_clip_demo项目。RK3588是瑞芯微（Rockchip）推出的高性能ARM处理器，广泛应用于Orange Pi 5、Orange Pi 5 Plus等开发板。

### 硬件要求

- **处理器**: RK3588 (aarch64架构)
- **内存**: 建议至少4GB RAM（推荐8GB或更多用于多通道演示）
- **存储**: 至少10GB可用存储空间
- **操作系统**: Ubuntu 22.04 LTS (aarch64) 或兼容的Linux发行版

### 支持的设备

- Orange Pi 5
- Orange Pi 5 Plus
- Orange Pi 5B
- Rock 5B
- 其他基于RK3588的开发板

### 前置条件

#### 1. 系统准备

确保您的RK3588设备运行Ubuntu 22.04或兼容系统：

```bash
# 检查系统版本
lsb_release -a

# 检查CPU架构
uname -m  # 应该显示 aarch64
```

#### 2. 获取DeepX Runtime (dx_rt)

dx_clip_demo项目依赖DeepX Runtime进行AI模型推理。您需要先获取并设置dx_rt：

```bash
# 假设dx_rt安装在以下路径
# 请根据实际情况调整路径
DXRT_PATH=/path/to/dx_rt
```

**注意**: DeepX Runtime的获取和安装请参考DeepX官方文档或联系DeepX技术支持。

### 构建步骤

#### 方法一：使用setup.sh自动设置（推荐）

这是最简单的方法，脚本会自动检测aarch64架构并进行相应配置。

##### OpenCV版本

```bash
# 克隆仓库
git clone https://github.com/Xu-feng-feng/dx_clip_demo.git
cd dx_clip_demo

# 运行设置脚本（OpenCV版本）
./setup.sh --app_type=opencv --dxrt_src_path=/path/to/dx_rt
```

##### PyQt版本

```bash
# 克隆仓库
git clone https://github.com/Xu-feng-feng/dx_clip_demo.git
cd dx_clip_demo

# 运行设置脚本（PyQt版本）
./setup.sh --app_type=pyqt --dxrt_src_path=/path/to/dx_rt
```

#### 方法二：使用scripts目录下的详细设置脚本

如果需要更多控制选项，可以直接使用scripts目录下的设置脚本：

```bash
# OpenCV版本
./scripts/setup_clip_demo_app.sh --app_type=opencv --arch_type=aarch64 --dxrt_src_path=/path/to/dx_rt

# PyQt版本
./scripts/setup_clip_demo_app.sh --app_type=pyqt --arch_type=aarch64 --dxrt_src_path=/path/to/dx_rt
```

### 安装过程说明

设置脚本会自动执行以下操作：

1. **检测系统架构**: 自动识别aarch64环境
2. **下载资源文件**: 
   - 预训练的CLIP AI模型
   - 示例视频文件
   - MSRVTT数据集（用于测试）
3. **创建Python虚拟环境**: 使用`--system-site-packages`选项以确保aarch64平台兼容性
4. **安装系统依赖**:
   - Python 3开发包
   - Qt5多媒体插件（PyQt版本需要）
   - GStreamer库（用于视频处理）
   - 其他必需的系统库
5. **安装Python包**: 
   - PyTorch (CPU版本)
   - OpenCV
   - PyQt5（如果选择PyQt版本）
   - CLIP模型依赖
6. **编译和安装DeepX Runtime Python包**

### 运行演示

#### 激活虚拟环境

```bash
# OpenCV版本
source venv-opencv/bin/activate

# PyQt版本
source venv-pyqt/bin/activate
```

#### 运行演示程序

##### OpenCV版本

1. **实时多通道演示**（16通道）:
```bash
python clip_demo_app_opencv/dx_realtime_multi_demo.py
```
按`q`键退出

2. **实时单通道演示**:
```bash
python clip_demo_app_opencv/dx_realtime_demo.py
```
- 添加文本: 在终端输入句子并按Enter
- 删除最后一句: 输入'del'并按Enter
- 退出程序: 输入'quit'并按Enter
- 相机模式: `python dx_realtime_demo.py --features_path 0`

3. **视频批处理演示**:
```bash
python clip_demo_app_opencv/dx_video_demo.py
```

##### PyQt版本

```bash
python -m clip_demo_app_pyqt.dx_realtime_demo_pyqt
```

### RK3588特定优化建议

#### 1. 内存管理

RK3588设备可能内存有限，建议：

```bash
# 监控内存使用
watch -n 1 free -h

# 如果内存不足，减少并发通道数
# 编辑配置或使用单通道模式
```

#### 2. CPU频率管理

为获得最佳性能，可以设置CPU为性能模式：

```bash
# 检查当前CPU调度器
cat /sys/devices/system/cpu/cpu*/cpufreq/scaling_governor

# 设置为性能模式（需要root权限）
sudo su
for cpu in /sys/devices/system/cpu/cpu*/cpufreq/scaling_governor; do
    echo performance > $cpu
done
exit
```

#### 3. 散热管理

RK3588在高负载下可能产生较多热量，建议：
- 使用主动散热（风扇）
- 监控温度：`cat /sys/class/thermal/thermal_zone0/temp`
- 考虑添加散热片

#### 4. 显示输出

对于无头（headless）部署：
- 使用OpenCV版本而非PyQt版本
- 通过SSH远程运行
- 考虑使用VNC或其他远程桌面方案

### 故障排除

#### 问题1: 虚拟环境创建失败

```bash
# 错误: venv创建失败
# 解决方案: 确保安装了python3-venv
sudo apt-get update
sudo apt-get install python3-venv python3-dev
```

#### 问题2: PyQt5安装问题

在aarch64平台，PyQt5可能需要特殊处理：

```bash
# 使用系统包管理器安装PyQt5
sudo apt-get install python3-pyqt5 python3-pyqt5.qtmultimedia

# 创建虚拟环境时使用--system-site-packages
python3 -m venv venv-pyqt --system-site-packages
```

#### 问题3: OpenCV导入错误

```bash
# 如果遇到OpenCV导入问题
# 安装依赖库
sudo apt-get install libgl1-mesa-glx libglib2.0-0
```

#### 问题4: DeepX Runtime找不到

```bash
# 确保DXRT_SRC_PATH正确
ls -la /path/to/dx_rt
# 应该看到dx_rt目录及其内容
# 确保包含python_package子目录
```

#### 问题5: 内存不足

```bash
# 为RK3588启用或增加swap空间
sudo fallocate -l 4G /swapfile
sudo chmod 600 /swapfile
sudo mkswap /swapfile
sudo swapon /swapfile

# 永久启用（添加到/etc/fstab）
echo '/swapfile none swap sw 0 0' | sudo tee -a /etc/fstab
```

#### 问题6: 视频播放性能问题

```bash
# 减少视频分辨率或通道数
# 使用硬件加速（如果GStreamer支持）
# 确保使用正确的视频编解码器
```

### 性能基准

在RK3588设备上的典型性能（参考值）：

- **单通道实时处理**: 15-25 FPS
- **4通道处理**: 8-15 FPS  
- **16通道处理**: 3-8 FPS

*注意: 实际性能取决于具体的硬件配置、散热情况和模型复杂度*

### 卸载

如果需要卸载或重新安装：

```bash
# 使用提供的卸载脚本
./uninstall.sh

# 手动清理（如果需要）
rm -rf venv-opencv venv-pyqt assets
```

### 其他资源

- 详细的OpenCV版本说明: [README-opencv.md](./README-opencv.md)
- 详细的PyQt版本说明: [README-pyqt.md](./README-pyqt.md)
- DeepX官方文档: https://sdk.deepx.ai/

### 技术支持

如遇到问题，请：
1. 查看上述故障排除部分
2. 检查GitHub Issues
3. 联系DeepX技术支持

---

## English Version

### Overview

This document provides detailed instructions for building and running the dx_clip_demo project on RK3588-based devices. The RK3588 is a high-performance ARM processor from Rockchip, widely used in development boards such as Orange Pi 5 and Orange Pi 5 Plus.

### Hardware Requirements

- **Processor**: RK3588 (aarch64 architecture)
- **Memory**: At least 4GB RAM recommended (8GB or more for multi-channel demos)
- **Storage**: At least 10GB available space
- **Operating System**: Ubuntu 22.04 LTS (aarch64) or compatible Linux distribution

### Supported Devices

- Orange Pi 5
- Orange Pi 5 Plus
- Orange Pi 5B
- Rock 5B
- Other RK3588-based development boards

### Prerequisites

#### 1. System Preparation

Ensure your RK3588 device is running Ubuntu 22.04 or a compatible system:

```bash
# Check system version
lsb_release -a

# Check CPU architecture
uname -m  # Should display aarch64
```

#### 2. Obtain DeepX Runtime (dx_rt)

The dx_clip_demo project depends on DeepX Runtime for AI model inference. You need to obtain and set up dx_rt first:

```bash
# Assume dx_rt is installed at the following path
# Adjust the path according to your actual situation
DXRT_PATH=/path/to/dx_rt
```

**Note**: Please refer to DeepX official documentation or contact DeepX technical support for obtaining and installing DeepX Runtime.

### Build Steps

#### Method 1: Using setup.sh for Automatic Setup (Recommended)

This is the simplest method. The script automatically detects the aarch64 architecture and configures accordingly.

##### OpenCV Version

```bash
# Clone repository
git clone https://github.com/Xu-feng-feng/dx_clip_demo.git
cd dx_clip_demo

# Run setup script (OpenCV version)
./setup.sh --app_type=opencv --dxrt_src_path=/path/to/dx_rt
```

##### PyQt Version

```bash
# Clone repository
git clone https://github.com/Xu-feng-feng/dx_clip_demo.git
cd dx_clip_demo

# Run setup script (PyQt version)
./setup.sh --app_type=pyqt --dxrt_src_path=/path/to/dx_rt
```

#### Method 2: Using Detailed Setup Script in scripts Directory

For more control options, use the setup script directly from the scripts directory:

```bash
# OpenCV version
./scripts/setup_clip_demo_app.sh --app_type=opencv --arch_type=aarch64 --dxrt_src_path=/path/to/dx_rt

# PyQt version
./scripts/setup_clip_demo_app.sh --app_type=pyqt --arch_type=aarch64 --dxrt_src_path=/path/to/dx_rt
```

### Installation Process Explanation

The setup script automatically performs the following operations:

1. **Detect System Architecture**: Automatically recognizes aarch64 environment
2. **Download Resource Files**: 
   - Pre-trained CLIP AI model
   - Sample video files
   - MSRVTT dataset (for testing)
3. **Create Python Virtual Environment**: Uses `--system-site-packages` option for aarch64 platform compatibility
4. **Install System Dependencies**:
   - Python 3 development packages
   - Qt5 multimedia plugins (required for PyQt version)
   - GStreamer libraries (for video processing)
   - Other required system libraries
5. **Install Python Packages**: 
   - PyTorch (CPU version)
   - OpenCV
   - PyQt5 (if PyQt version is selected)
   - CLIP model dependencies
6. **Build and Install DeepX Runtime Python Package**

### Running the Demo

#### Activate Virtual Environment

```bash
# OpenCV version
source venv-opencv/bin/activate

# PyQt version
source venv-pyqt/bin/activate
```

#### Run Demo Programs

##### OpenCV Version

1. **Real-time Multi-channel Demo** (16 channels):
```bash
python clip_demo_app_opencv/dx_realtime_multi_demo.py
```
Press `q` to exit

2. **Real-time Single-channel Demo**:
```bash
python clip_demo_app_opencv/dx_realtime_demo.py
```
- Add text: Type sentence in terminal and press Enter
- Delete last sentence: Type 'del' and press Enter
- Exit program: Type 'quit' and press Enter
- Camera mode: `python dx_realtime_demo.py --features_path 0`

3. **Video Batch Demo**:
```bash
python clip_demo_app_opencv/dx_video_demo.py
```

##### PyQt Version

```bash
python -m clip_demo_app_pyqt.dx_realtime_demo_pyqt
```

### RK3588-Specific Optimization Recommendations

#### 1. Memory Management

RK3588 devices may have limited memory. Recommendations:

```bash
# Monitor memory usage
watch -n 1 free -h

# If memory is insufficient, reduce concurrent channels
# Edit configuration or use single-channel mode
```

#### 2. CPU Frequency Management

For optimal performance, set CPU to performance mode:

```bash
# Check current CPU scheduler
cat /sys/devices/system/cpu/cpu*/cpufreq/scaling_governor

# Set to performance mode (requires root)
sudo su
for cpu in /sys/devices/system/cpu/cpu*/cpufreq/scaling_governor; do
    echo performance > $cpu
done
exit
```

#### 3. Thermal Management

RK3588 may generate significant heat under high load. Recommendations:
- Use active cooling (fan)
- Monitor temperature: `cat /sys/class/thermal/thermal_zone0/temp`
- Consider adding heatsink

#### 4. Display Output

For headless deployment:
- Use OpenCV version instead of PyQt version
- Run remotely via SSH
- Consider using VNC or other remote desktop solutions

### Troubleshooting

#### Issue 1: Virtual Environment Creation Failed

```bash
# Error: venv creation failed
# Solution: Ensure python3-venv is installed
sudo apt-get update
sudo apt-get install python3-venv python3-dev
```

#### Issue 2: PyQt5 Installation Problems

On aarch64 platforms, PyQt5 may require special handling:

```bash
# Install PyQt5 using system package manager
sudo apt-get install python3-pyqt5 python3-pyqt5.qtmultimedia

# Create virtual environment with --system-site-packages
python3 -m venv venv-pyqt --system-site-packages
```

#### Issue 3: OpenCV Import Error

```bash
# If encountering OpenCV import issues
# Install dependency libraries
sudo apt-get install libgl1-mesa-glx libglib2.0-0
```

#### Issue 4: DeepX Runtime Not Found

```bash
# Ensure DXRT_SRC_PATH is correct
ls -la /path/to/dx_rt
# Should see dx_rt directory and its contents
# Ensure it contains python_package subdirectory
```

#### Issue 5: Out of Memory

```bash
# Enable or increase swap space for RK3588
sudo fallocate -l 4G /swapfile
sudo chmod 600 /swapfile
sudo mkswap /swapfile
sudo swapon /swapfile

# Enable permanently (add to /etc/fstab)
echo '/swapfile none swap sw 0 0' | sudo tee -a /etc/fstab
```

#### Issue 6: Video Playback Performance Issues

```bash
# Reduce video resolution or number of channels
# Use hardware acceleration (if GStreamer supports)
# Ensure correct video codec is used
```

### Performance Benchmarks

Typical performance on RK3588 devices (reference values):

- **Single-channel real-time processing**: 15-25 FPS
- **4-channel processing**: 8-15 FPS  
- **16-channel processing**: 3-8 FPS

*Note: Actual performance depends on specific hardware configuration, cooling conditions, and model complexity*

### Uninstallation

To uninstall or reinstall:

```bash
# Use provided uninstall script
./uninstall.sh

# Manual cleanup (if needed)
rm -rf venv-opencv venv-pyqt assets
```

### Additional Resources

- Detailed OpenCV version documentation: [README-opencv.md](./README-opencv.md)
- Detailed PyQt version documentation: [README-pyqt.md](./README-pyqt.md)
- DeepX official documentation: https://sdk.deepx.ai/

### Technical Support

If you encounter issues:
1. Check the troubleshooting section above
2. Review GitHub Issues
3. Contact DeepX technical support

---

## 版本历史 / Version History

- v1.0 (2025-01): Initial RK3588 build guide
