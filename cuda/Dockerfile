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
# ②-2 安装 R 环境及 Cardinal
# ------------------------------
# update indices & install helper packages
RUN apt-get update -qq && \
    apt-get install -y --no-install-recommends software-properties-common dirmngr && \
    # add the signing key
    wget -qO- https://cloud.r-project.org/bin/linux/ubuntu/marutter_pubkey.asc | tee -a /etc/apt/trusted.gpg.d/cran_ubuntu_key.asc && \
    # add the repo from CRAN
    add-apt-repository "deb https://cloud.r-project.org/bin/linux/ubuntu $(lsb_release -cs)-cran40/" && \
    # install R itself
    apt-get update && apt-get install -y --no-install-recommends r-base && \
    # Install BiocManager and Cardinal
    R -e 'if (!require("BiocManager", quietly = TRUE)) install.packages("BiocManager"); BiocManager::install("Cardinal")' && \
    apt-get clean && rm -rf /var/lib/apt/lists/* /tmp/* /var/tmp/*

# ------------------------------
# ③ 输出cuda的详细信息
# ------------------------------
RUN nvcc --version | sed -n 's/.*release \(.*\),.*/\1/p' && \
    R -e 'cat(as.character(packageVersion("Cardinal")), "\n")'

# ------------------------------
# 默认命令
# ------------------------------
WORKDIR /home/Share_Space
CMD ["/bin/bash"]