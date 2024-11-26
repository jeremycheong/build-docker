ARG VERSION="22.12"
FROM nvcr.io/nvidia/pytorch:${VERSION}-py3
# 注意：ARG 指令有生效范围，如果在 FROM 指令之前指定，那么只能用于 FROM 指令中
ARG USER_NAME=zhangym
ARG GUID=1000

# (Optional, use Mirror to speed up downloads)
# RUN rm -f /etc/apt/sources.list.d/*.list &&\
#     apt-get update --fix-missing

RUN rm -f /etc/apt/sources.list.d/*.list && \ 
    sed -i 's@//.*archive.ubuntu.com@//mirrors.aliyun.com@g' /etc/apt/sources.list && \
    sed -i 's/security.ubuntu.com/mirrors.ustc.edu.cn/g' /etc/apt/sources.list && \
    sed -i 's/http:/https:/g' /etc/apt/sources.list && \
    apt-get update --fix-missing
#    pip config set global.index-url https://pypi.tuna.tsinghua.edu.cn/simple

# Install the required packages
RUN apt-get install -y sudo vim openssh-server && \
    apt-get clean && \
    mkdir /var/run/sshd && \
    rm -rf /var/lib/apt/lists/* && \
    pip config set global.index-url http://mirrors.aliyun.com/pypi/simple && \
    pip config set install.trusted-host mirrors.aliyun.com 

# Create a non-root user and switch to it
RUN addgroup --gid ${GUID} ${USER_NAME} && \
    adduser --home /home/${USER_NAME} --shell /bin/bash --disabled-password --uid ${GUID} --gid ${GUID} --gecos '' ${USER_NAME} && \
    adduser ${USER_NAME} sudo && \
    echo "${USER_NAME} ALL=(ALL) NOPASSWD:ALL" >> /etc/sudoers.d/90-user
    

# use non-root user
USER ${USER_NAME}

# Install python packages
RUN python -m pip install -U --no-cache-dir --upgrade pip && \    
    pip install -U --no-cache-dir yacs scikit-learn opencv-python-headless  
    # /home/${USER_NAME}/.local/bin/mim install mmengine mmcv

# Create a working directory
RUN mkdir /home/${USER_NAME}/workspace 
WORKDIR /home/${USER_NAME}/workspace
COPY --chown=${USER_NAME}:${USER_NAME} ./app ./app

# Set and clear user env
RUN cp ./app/basic.vim /home/${USER_NAME}/.vimrc && \
    mkdir /home/${USER_NAME}/.ssh && cp ./app/authorized_keys /home/${USER_NAME}/.ssh && \
    sed -i 's/#force_color_prompt=yes/force_color_prompt=yes/g' /home/${USER_NAME}/.bashrc && \ 
    rm -rf ./app && \    
    sudo rm -rf /tmp/*
    
ENV PATH=/home/${USER_NAME}/.local/bin:$PATH \
    FORCE_CUDA="1" \
    LANG=C.UTF-8 \
    TimeZone=Asia/Shanghai

CMD ["sudo", "/usr/sbin/sshd", "-D" ]
