# ==============================
# Stage 1: Miniconda 构建阶段
# ==============================
FROM nvidia/cuda:11.3.1-cudnn8-runtime-ubuntu18.04 AS conda-builder

ENV LANG=C.UTF-8 \
    LC_ALL=C.UTF-8 \
    TZ=Asia/Kolkata \
    DEBIAN_FRONTEND=noninteractive

# 安装基础依赖并下载 Miniconda
RUN apt-get update && apt-get install -y --no-install-recommends \
        wget bzip2 ca-certificates && \
    wget -q https://repo.anaconda.com/miniconda/Miniconda3-py39_4.11.0-Linux-x86_64.sh -O /tmp/miniconda.sh && \
    bash /tmp/miniconda.sh -b -p /opt/conda && \
    rm -rf /var/lib/apt/lists/* /tmp/*

# ==============================
# Stage 2: 主镜像阶段
# ==============================
FROM nvidia/cuda:13.0.1-cudnn-runtime-ubuntu24.04

ENV LANG=C.UTF-8 \
    LC_ALL=C.UTF-8 \
    TZ=Asia/Kolkata \
    DEBIAN_FRONTEND=noninteractive \
    PATH=/opt/conda/bin:$PATH

# 复制 Miniconda
COPY --from=conda-builder /opt/conda /opt/conda

# ------------------------------
# ① 安装系统工具和 git-lfs
# ------------------------------
RUN apt-get update && apt-get install -y --no-install-recommends \
        sudo git git-lfs ssh vim wget curl htop less ca-certificates && \
    git lfs install && \
    echo "PermitRootLogin yes" >> /etc/ssh/sshd_config && \
    mkdir -p /home/Share_Space && chmod 777 /home/Share_Space && \
    apt-get clean && rm -rf /var/lib/apt/lists/* /tmp/* /var/tmp/*

# ------------------------------
# ② 在 Conda 环境中使用 pip 安装 Python 库
# ------------------------------
RUN /opt/conda/bin/python -m pip install --upgrade pip && \
    /opt/conda/bin/pip install numpy pandas && \
    /opt/conda/bin/pip install scipy scikit-learn matplotlib && \
    /opt/conda/bin/pip install torch torchvision --index-url https://download.pytorch.org/whl/cu129 && \
    /opt/conda/bin/pip install pytorch-lightning timm transformers datasets && \
    /opt/conda/bin/pip cache purge

# ------------------------------
# 默认命令
# ------------------------------
CMD ["/bin/bash"]

