ARG DOCKER_REGISTRY=public.aml-repo.cms.waikato.ac.nz:443/
FROM ${DOCKER_REGISTRY}ubuntu:24.04

ENV DEBIAN_FRONTEND=noninteractive
RUN apt-get update && apt-get install -y --no-install-recommends git libglib2.0-0 libsm6 libxrender-dev libxext6 libglx-mesa0 python3-dev python3-pip python3-venv wget clang cmake build-essential \
    && apt-get clean \
    && rm -rf /var/lib/apt/lists/*

ENV CMAKE_C_COMPILER=/usr/bin/clang
ENV CMAKE_CXX_COMPILER=/usr/bin/clang++

WORKDIR /opt

RUN git clone --recursive https://github.com/microsoft/BitNet.git && \
    cd BitNet && \
    git reset --hard c17d1c5d77c48af7d6fb29c9f28a3da0277fc394
RUN python3 -m venv /opt/BitNet.env && \
    cd /opt/BitNet && \
    /opt/BitNet.env/bin/pip install --no-cache-dir -r requirements.txt && \
    /opt/BitNet.env/bin/pip install --no-cache-dir -U "huggingface_hub[cli]"
# call setup_env.py to compile llama.cpp
RUN cd /opt/BitNet && \
    /opt/BitNet.env/bin/huggingface-cli download microsoft/BitNet-b1.58-2B-4T-gguf --local-dir models/BitNet-b1.58-2B-4T && \
    /opt/BitNet.env/bin/python setup_env.py -md models/BitNet-b1.58-2B-4T -q i2_s && \
    rm -Rf models

COPY bash.bashrc /etc/bash.bashrc
COPY bitnet_* /usr/bin/
COPY huggingface* /usr/bin/

ENV PYTHONPATH=/opt/BitNet

WORKDIR /opt/BitNet
