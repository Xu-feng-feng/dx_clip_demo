# RK3588快速开始指南 / RK3588 Quick Start Guide

[English Version Below](#english-version)

## 中文版本

### 快速构建（5分钟上手）

#### 前置准备

1. **RK3588设备**（如Orange Pi 5/5+）运行Ubuntu 22.04
2. **DeepX Runtime** 已安装在 `/path/to/dx_rt`
3. **至少4GB可用内存**和10GB存储空间

#### 一键构建

```bash
# 1. 克隆仓库
git clone https://github.com/Xu-feng-feng/dx_clip_demo.git
cd dx_clip_demo

# 2. 运行设置（选择OpenCV或PyQt版本）
# OpenCV版本（推荐用于RK3588）
./setup.sh --app_type=opencv --dxrt_src_path=/path/to/dx_rt

# 或PyQt版本（需要更多资源）
./setup.sh --app_type=pyqt --dxrt_src_path=/path/to/dx_rt

# 3. 激活虚拟环境
source venv-opencv/bin/activate  # 或 venv-pyqt

# 4. 运行演示
python clip_demo_app_opencv/dx_realtime_demo.py
```

### 常见问题快速解决

| 问题 | 快速解决 |
|------|---------|
| 内存不足 | `sudo fallocate -l 4G /swapfile && sudo mkswap /swapfile && sudo swapon /swapfile` |
| PyQt5安装失败 | `sudo apt-get install python3-pyqt5 python3-pyqt5.qtmultimedia` |
| 性能不佳 | 设置CPU为性能模式: `echo performance \| sudo tee /sys/devices/system/cpu/cpu*/cpufreq/scaling_governor` |
| 过热 | 添加散热风扇或降低通道数 |

### 推荐配置

- **入门**: 单通道模式，OpenCV版本
- **标准**: 4通道，8GB RAM，主动散热
- **高级**: 16通道，16GB RAM，主动散热+散热片

### 详细文档

完整的构建说明、优化技巧和故障排除，请参阅：
- [完整RK3588构建指南](../README-RK3588.md)
- [OpenCV版本说明](../README-opencv.md)
- [PyQt版本说明](../README-pyqt.md)

---

## English Version

### Quick Build (5-Minute Setup)

#### Prerequisites

1. **RK3588 device** (e.g., Orange Pi 5/5+) running Ubuntu 22.04
2. **DeepX Runtime** installed at `/path/to/dx_rt`
3. **At least 4GB RAM** and 10GB storage available

#### One-Command Build

```bash
# 1. Clone repository
git clone https://github.com/Xu-feng-feng/dx_clip_demo.git
cd dx_clip_demo

# 2. Run setup (choose OpenCV or PyQt version)
# OpenCV version (recommended for RK3588)
./setup.sh --app_type=opencv --dxrt_src_path=/path/to/dx_rt

# Or PyQt version (requires more resources)
./setup.sh --app_type=pyqt --dxrt_src_path=/path/to/dx_rt

# 3. Activate virtual environment
source venv-opencv/bin/activate  # or venv-pyqt

# 4. Run demo
python clip_demo_app_opencv/dx_realtime_demo.py
```

### Quick Problem Solutions

| Issue | Quick Fix |
|-------|-----------|
| Out of memory | `sudo fallocate -l 4G /swapfile && sudo mkswap /swapfile && sudo swapon /swapfile` |
| PyQt5 install fails | `sudo apt-get install python3-pyqt5 python3-pyqt5.qtmultimedia` |
| Poor performance | Set CPU to performance mode: `echo performance \| sudo tee /sys/devices/system/cpu/cpu*/cpufreq/scaling_governor` |
| Overheating | Add cooling fan or reduce channel count |

### Recommended Configurations

- **Beginner**: Single-channel mode, OpenCV version
- **Standard**: 4 channels, 8GB RAM, active cooling
- **Advanced**: 16 channels, 16GB RAM, active cooling + heatsink

### Detailed Documentation

For complete build instructions, optimization tips, and troubleshooting:
- [Complete RK3588 Build Guide](../README-RK3588.md)
- [OpenCV Version Documentation](../README-opencv.md)
- [PyQt Version Documentation](../README-pyqt.md)
