# 介绍

该镜像专为高性能计算的Massflow框架设计，基于 NVIDIA CUDA 官方镜像构建，集成了新一代 Python 包管理工具 uv 以及 R 语言环境。

## 包含的工具集

### 系统开发工具与编译工具

- **编译链**: GCC, GFortran, pkg-config, libc6-dev。
- **数学与科学库**: BLAS, LAPACK, FFTW3 (快速傅里叶变换)。
- **图像与数据库**: LibTIFF, LibJPEG, LibPNG, LibXML2, LibCurl (OpenSSL)。
- **版本控制**: Git (预装 Git LFS，支持大文件版本控制)。
- **远程连接**: OpenSSH Server (配置为允许 Root 登录，便于远程开发)。
- **常用工具**: `vim`, `wget`, `curl`, `htop`, `less`, `bzip2`, `sudo`.

###  编程语言与包管理

1、内置 uv (极速 Python 包管理器和解析器)，用于替代传统的 pip/virtualenv 工作流。

2、R 语言环境:

- 版本: R 4.5.2+
- 核心库: 集成 BiocManager 与Cardinal

### 镜像 Tag 命名规则

该镜像的 Tag 是动态生成的，用于直观地展示关键组件的版本信息。

**Tag 格式:**`ub<Ubuntu版本>_cuda<CUDA主次版本>_cardinal<Cardinal主次版本>`

| 组成部分     | 含义         | 说明                                   |
| ------------ | ------------ | -------------------------------------- |
| `ub24`       | Ubuntu 24.x  | 基于 Ubuntu 24.04 构建                 |
| `cuda131`    | CUDA 13.1    | CUDA 编译工具链版本                    |
| `cardinal312` | Cardinal 3.12 | R 语言 Cardinal 包的大版本号和小版本号 |

### 使用说明

- **工作目录**: 默认进入 `/home/Share_Space`，且该目录权限已设为 `777`，方便挂载外部卷。
- **SSH 服务**: 镜像内已安装 SSH 服务并创建了运行目录 `/var/run/sshd`，可通过端口映射进行远程开发连接。
- **版本检查**: 镜像根目录下有一个 `/version.txt` 文件，记录了构建时的具体版本快照。

```bash
docker run -it \
  --gpus all \
  -v "${HOME}/Share_Space:/home/Share_Space" \
  -p 8001:22 \
  --rm \
  neonexusx/massflow:latest
```

