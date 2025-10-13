FROM nvcr.io/nvidia/pytorch:24.08-py3

ENV DEBIAN_FRONTEND=noninteractive

# 安装基础工具（已加入git-lfs）
RUN apt-get update && \
    apt-get install -y --no-install-recommends \
        openssh-client \
        openssh-server \
        git \
        git-lfs \
        vim \
        wget \
        bzip2 && \
    apt-get clean && \
    # 清理临时目录
    rm -rf /var/lib/apt/lists/* /tmp/* /var/tmp/* && \
    # 确保SSH运行时目录存在
    mkdir -p /var/run/sshd && \
    echo "PermitRootLogin yes" >> /etc/ssh/sshd_config && \
    mkdir -p /home/Share_Space && \
    chmod 777 /home/Share_Space && \
    git lfs install  # 初始化Git LFS

# 设置工作目录
WORKDIR /home/Share_Space

# 安装 Python 包 (使用清华PyPI镜像加速)
RUN pip install --no-cache-dir -i https://pypi.tuna.tsinghua.edu.cn/simple lightning

# 默认命令
CMD ["/bin/bash"]

