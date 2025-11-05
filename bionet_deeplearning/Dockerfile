# ==============================
# Stage 1: Miniconda 构建阶段
# ==============================
FROM nvidia/cuda:13.0.1-cudnn-runtime-ubuntu24.04 AS conda-builder

ENV LANG=C.UTF-8 \
    LC_ALL=C.UTF-8 \
    TZ=Asia/Kolkata \
    DEBIAN_FRONTEND=noninteractive

# 安装基础依赖并下载 Miniconda
RUN apt-get update && apt-get install -y --no-install-recommends wget ca-certificates && \
    wget -q https://repo.anaconda.com/miniconda/Miniconda3-latest-Linux-x86_64.sh -O /tmp/miniconda.sh && \
    bash /tmp/miniconda.sh -b -p /opt/conda && \
    rm -rf /var/lib/apt/lists/* /tmp/*

# ==============================
# Stage 2: 主镜像阶段
# ==============================
FROM nvidia/cuda:13.0.1-cudnn-runtime-ubuntu24.04

ENV LANG=C.UTF-8 \
    LC_ALL=C.UTF-8 \
    TZ=Asia/Shanghai \
    DEBIAN_FRONTEND=noninteractive \
    PATH=/opt/conda/bin:$PATH

# 复制 Miniconda
COPY --from=conda-builder /opt/conda /opt/conda

# ------------------------------
# ① 安装系统工具和 git-lfs
# ------------------------------
RUN apt-get update && apt-get install -y --no-install-recommends \
        sudo git git-lfs vim wget curl htop less bzip2 ssh wget ca-certificates && \
    git lfs install && \
    mkdir -p /home/Share_Space && chmod 777 /home/Share_Space && \
    apt-get clean && rm -rf /var/lib/apt/lists/* /tmp/* /var/tmp/*

# ------------------------------
# ② 在 Conda 环境中使用 pip 安装 Python 库
# ------------------------------
RUN /opt/conda/bin/python -m pip install --upgrade pip && \
    /opt/conda/bin/pip install --no-cache-dir numpy pandas && \
    /opt/conda/bin/pip install --no-cache-dir scipy scikit-learn matplotlib && \
    /opt/conda/bin/pip install --no-cache-dir torch torchvision --index-url https://download.pytorch.org/whl/cu130 && \
    /opt/conda/bin/pip install --no-cache-dir pytorch-lightning timm transformers datasets && \
    /opt/conda/bin/pip cache purge

# ------------------------------
# ③ 输出Python包详细信息
# ------------------------------
# 记录主要包和 CUDA/CUDNN 版本到 /version.txt
RUN /opt/conda/bin/python -c "import torch, timm, pytorch_lightning as pl; \
    print('cuda=' + (torch.version.cuda or 'None')); \
    print('torch=' + torch.__version__); \
    print('lightning=' + pl.__version__); \
    print('timm=' + timm.__version__);" > /version.txt

# ------------------------------
# 默认命令
# ------------------------------
WORKDIR /home/Share_Space
CMD ["/bin/bash"]