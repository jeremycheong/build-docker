ARG CUDA="10.2"
ARG CUDNN="7"
FROM nvidia/cuda:${CUDA}-cudnn${CUDNN}-devel-ubuntu18.04
# 注意：ARG 指令有生效范围，如果在 FROM 指令之前指定，那么只能用于 FROM 指令中
ARG TORCHAUDIOVERSION="0.12.1"
ARG TORCHVISIONVERSION="0.13.1"
ARG TORCHVERSION="1.12.1"
ARG CUVERSION="102"
ARG USERNAME=zhangym
ARG GUID=1000
ARG MINICONDA_ROOT=/usr/local/miniconda3

# (Optional, use Mirror to speed up downloads)
RUN rm -f /etc/apt/sources.list.d/*.list && \ 
    sed -i 's@//.*archive.ubuntu.com@//mirrors.ustc.edu.cn@g' /etc/apt/sources.list && \
    sed -i 's/security.ubuntu.com/mirrors.ustc.edu.cn/g' /etc/apt/sources.list && \
    sed -i 's/http:/https:/g' /etc/apt/sources.list

# Install the required packages
RUN apt-get update && \
    apt-get install -y sudo vim openssh-server build-essential ca-certificates libgl1-mesa-glx&& \
    apt-get clean && \
    mkdir /var/run/sshd && \
    rm -rf /var/lib/apt/lists/*

# Create a non-root user and switch to it
RUN adduser --home /home/${USERNAME} --shell /bin/bash --disabled-password --gecos '' ${USERNAME} && \
    adduser ${USERNAME} sudo && \
    echo "${USERNAME} ALL=(ALL) NOPASSWD:ALL" >> /etc/sudoers.d/90-user && \
    usermod -u ${GUID} ${USERNAME} && usermod -G ${GUID} ${USERNAME}

# use non-root user
USER ${USERNAME}

# Create a working directory
RUN mkdir /home/${USERNAME}/workspace 
WORKDIR /home/${USERNAME}/workspace
COPY --chown=${USERNAME}:${USERNAME} ./app ./app

# Install python packages
# RUN sudo bash app/Miniconda3.sh -b -p ${MINICONDA_ROOT} && \
#     ${MINICONDA_ROOT}/bin/pip config set global.index-url http://mirrors.aliyun.com/pypi/simple && \
#     ${MINICONDA_ROOT}/bin/pip config set install.trusted-host mirrors.aliyun.com && \
#     ${MINICONDA_ROOT}/bin/python -m pip install -U --no-cache-dir --upgrade pip && \
#     /home/${USERNAME}/.local/bin/pip install -U --no-cache-dir torch==1.12.1+cu102 torchvision==0.13.1+cu102 torchaudio==0.12.1 --extra-index-url https://download.pytorch.org/whl/cu102 && \
#     /home/${USERNAME}/.local/bin/pip install -U --no-cache-dir openmim yacs scikit-learn opencv-python-headless && \    
#     /home/${USERNAME}/.local/bin/mim install mmengine mmcv

RUN sudo bash app/Miniconda3.sh -b -p ${MINICONDA_ROOT} && \
    ${MINICONDA_ROOT}/bin/pip config set global.index-url http://mirrors.aliyun.com/pypi/simple && \
    ${MINICONDA_ROOT}/bin/pip config set install.trusted-host mirrors.aliyun.com && \
    ${MINICONDA_ROOT}/bin/python -m pip install -U --no-cache-dir --upgrade pip && \
    /home/${USERNAME}/.local/bin/pip install -U --no-cache-dir app/torch-${TORCHVERSION}+cu${CUVERSION}-cp39-cp39-linux_x86_64.whl && \
    /home/${USERNAME}/.local/bin/pip install -U --no-cache-dir app/torchvision-${TORCHVISIONVERSION}+cu${CUVERSION}-cp39-cp39-linux_x86_64.whl && \
    /home/${USERNAME}/.local/bin/pip install -U --no-cache-dir app/torchaudio-${TORCHAUDIOVERSION}+cu${CUVERSION}-cp39-cp39-linux_x86_64.whl && \
    /home/${USERNAME}/.local/bin/pip install -U --no-cache-dir openmim yacs scikit-learn opencv-python-headless && \    
    /home/${USERNAME}/.local/bin/mim install mmengine mmcv

# Set and clear user env
RUN cp ./app/basic.vim /home/${USERNAME}/.vimrc && \
    mkdir /home/${USERNAME}/.ssh && cp ./app/authorized_keys /home/${USERNAME}/.ssh && \
    sed -i 's/#force_color_prompt=yes/force_color_prompt=yes/g' /home/${USERNAME}/.bashrc && \
    rm -rf ./app && \         
    sudo rm -rf /tmp/*
    
ENV PATH=/usr/local/miniconda3/bin:/home/${USERNAME}/.local/bin:$PATH \
    FORCE_CUDA="1" \
    LANG=C.UTF-8

CMD [ "/usr/sbin/sshd", "-D" ]


