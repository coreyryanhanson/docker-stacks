# Copyright (c) Jupyter Development Team.
# Distributed under the terms of the Modified BSD License.
# This is a fork of Jupyter's Dockerfile repository with minimal modifications to the base docker image.

ARG BASE_CONTAINER=coreyhanson/anacuda-scipy
FROM $BASE_CONTAINER

# Modifications to turn NVIDIA runtime based jupyter image to contain common runtime dependencies to Tensorflow and PyTorch.
#From https://github.com/tensorflow/tensorflow/blob/master/tensorflow/tools/dockerfiles/dockerfiles/gpu.Dockerfile
# https://github.com/pytorch/pytorch/blob/master/docker/pytorch/ubuntu_cpu_gpu/Dockerfile

USER root

ARG UBUNTU_VERSION=18.04

ENV ARCH=
ENV CUDA 11.1
ENV CUDNN=8.0.5.39
ENV CUDNN_VERSION="${CUDNN}"
ENV CUDNN_MAJOR_VERSION=8
ENV LIB_DIR_PREFIX=x86_64
ENV LIBNVINFER=7.2.2-1
ENV LIBNVINFER_MAJOR_VERSION=7
ENV NCCL_VERSION 2.8.4

LABEL com.nvidia.cudnn.version="${CUDNN_VERSION}"

RUN apt-get update && apt-get install -y --no-install-recommends \
    build-essential \
    cuda-command-line-tools-${CUDA/./-} \
    libcublas-${CUDA/./-} \
    libcublas-dev-${CUDA/./-} \
    cuda-nvrtc-${CUDA/./-} \
    libcufft-${CUDA/./-} \
    libcurand-${CUDA/./-} \
    libcusolver-${CUDA/./-} \
    libcusparse-${CUDA/./-} \
    libcudnn8=$CUDNN_VERSION-1+cuda${CUDA/./-} \
    libfreetype6-dev \
    libhdf5-serial-dev \
    libzmq3-dev \
    pkg-config \
    software-properties-common \
    ca-certificates \
    curl \
&& \
    rm -rf /var/lib/apt/lists/*

ENV LIBRARY_PATH /usr/local/cuda/lib64/stubs

USER $NB_UID
