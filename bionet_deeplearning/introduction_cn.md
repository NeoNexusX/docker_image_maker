# 介绍

该镜像面向 BioNet 深度学习任务，基于 NVIDIA CUDA 官方运行时镜像构建，采用 Miniconda + pip 的方式预装常用机器学习与深度学习依赖。

## 包含的工具集

### 基础系统工具与编译环境

- 版本控制: Git（预装 Git LFS，支持大文件版本管理）
- 远程连接: OpenSSH Server（已配置允许 Root 登录）
- 编译与底层库: `pkg-config`, `python3-dev`, `gfortran`, `libblas-dev`, `liblapack-dev`（支持复杂科学计算库的源码编译）
- 常用工具: `sudo`, `vim`, `wget`, `curl`, `htop`, `less`, `bzip2`, `ca-certificates`

### Python 与深度学习环境

- 环境管理: Miniconda（安装在 `/opt/conda`，并加入 `PATH`）
- Python 包管理: pip（构建时升级至最新）
- 预装科学计算库: `numpy`, `pandas`, `scipy`, `scikit-learn`, `matplotlib`
- 预装深度学习库:
  - `torch`, `torchvision`（通过 PyTorch 官方 CUDA 13.0 源安装）
  - `pytorch-lightning`
  - `timm`
  - `transformers`
  - `datasets`
- Python 辅助工具: `uv`, `uvx`

### 基础镜像与版本信息

- 基础镜像: `nvidia/cuda:13.0.1-cudnn-runtime-ubuntu24.04`
- 时区: `Asia/Shanghai`（主镜像）
- 版本快照: 构建时会将关键版本写入 `/version.txt`，包括:
  - `cuda`（来自 `torch.version.cuda`）
  - `torch`
  - `lightning`
  - `timm`
  - `uv`

## 使用说明

- 工作目录: 默认进入 `/home/Share_Space`，且目录权限为 `777`
- SSH 服务: 镜像内已创建 `/var/run/sshd` 并开启 Root 登录配置
- Conda 路径: `/opt/conda/bin` 已加入环境变量 `PATH`

```bash
docker run -it \
  --gpus all \
  -v "${HOME}/Share_Space:/home/Share_Space" \
  -p 8001:22 \
  --rm \
  neonexusx/bionet_deeplearning:latest
```

使用ssh连接建议先执行:

```bash
service ssh restart && passwd
```

快速验证 PyTorch CUDA 可用性:

```bash
python -c "import torch; print(torch.__version__); print(torch.version.cuda); print(torch.cuda.is_available())"
```
