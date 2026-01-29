# ==============================
# Stage 1:uv构建阶段
# ==============================
FROM ghcr.io/astral-sh/uv:latest AS uv-builder

# ==============================
# Stage 2: 主镜像阶段
# ==============================
FROM nvidia/cuda:11.7.1-devel-ubuntu20.04

ENV LANG=C.UTF-8 \
    LC_ALL=C.UTF-8 \
    TZ=Asia/Shanghai \
    DEBIAN_FRONTEND=noninteractive \
    PATH=/opt/conda/bin:$PATH

# ------------------------------
# ① 安装系统工具和 git-lfs
# ------------------------------
RUN apt-get update && apt-get install -y --no-install-recommends \
        sudo git git-lfs vim wget curl htop less bzip2 ssh wget ca-certificates && \
    git lfs install && \
    mkdir -p /home/Share_Space && chmod 777 /home/Share_Space && \
    mkdir -p /var/run/sshd && \
    echo "PermitRootLogin yes" >> /etc/ssh/sshd_config && \
    apt-get clean && rm -rf /var/lib/apt/lists/* /tmp/* /var/tmp/*

# ------------------------------
# ② 安装uv环境
# ------------------------------
COPY --from=uv-builder /uv /usr/local/bin/uv
COPY --from=uv-builder /uvx /usr/local/bin/uvx

# ------------------------------
# ③ 输出cuda的详细信息
# ------------------------------
RUN nvcc --version | sed -n 's/.*release \(.*\),.*/\1/p'

# ------------------------------
# 默认命令
# ------------------------------
WORKDIR /home/Share_Space
CMD ["/bin/bash"]