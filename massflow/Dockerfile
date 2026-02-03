# ==============================
# Stage 1:uv构建阶段
# ==============================
FROM ghcr.io/astral-sh/uv:latest AS uv-builder

# ==============================
# Stage 2: 主镜像阶段
# ==============================
FROM nvidia/cuda:13.0.2-cudnn-devel-ubuntu24.04

ENV LANG=C.UTF-8 \
    LC_ALL=C.UTF-8 \
    TZ=Asia/Shanghai \
    DEBIAN_FRONTEND=noninteractive \
    PATH=/opt/conda/bin:$PATH

# ------------------------------
# ① 安装系统工具和 git-lfs。 libc6-dev 老ubuntu使用
# ------------------------------
RUN apt-get update && apt-get install -y --no-install-recommends \
        sudo git git-lfs vim wget curl htop less bzip2 ssh wget ca-certificates \
        pkg-config \
        libc6-dev \
        gfortran \
        libblas-dev \
        liblapack-dev \
        libtiff5-dev \
        libjpeg-dev \
        libpng-dev \
        libfftw3-dev \
        libcurl4-openssl-dev \
        libpcre2-dev \
        libxml2-dev \
        libbz2-dev \
        libtirpc-dev \
        python3-dev && \
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
    apt-get update && apt-get install -y --no-install-recommends r-base=4.5.2* && \
    # Install BiocManager and Cardinal
    R -e 'if (!require("BiocManager", quietly = TRUE)) install.packages("BiocManager"); Sys.setenv(PKG_LIBS = "-lrt"); BiocManager::install(c("matter", "CardinalIO"), force = TRUE); BiocManager::install("Cardinal")' && \
    apt-get clean && rm -rf /var/lib/apt/lists/* /tmp/* /var/tmp/*

# ------------------------------
# ③ 输出版本详细信息到 /version.txt
# ------------------------------
RUN echo "ubuntu=$(lsb_release -rs | cut -d. -f1)" > /version.txt && \
    echo "cuda=$(nvcc --version | sed -n 's/.*release \([0-9]*\)\.\([0-9]*\).*/\1\2/p')" >> /version.txt && \
    echo "cardinal=$(R --slave -e 'v<-packageVersion("Cardinal"); cat(v$major, v$minor, sep="")')" >> /version.txt

# ------------------------------
# 默认命令
# ------------------------------
WORKDIR /home/Share_Space
CMD ["/bin/bash"]