# Copyright (c) Jupyter Development Team.
# Distributed under the terms of the Modified BSD License.
# This is a fork of Jupyter's Dockerfile repository with minimal modifications to the base docker image.

ARG BASE_CONTAINER=coreyhanson/anacuda-scipy
FROM $BASE_CONTAINER

# Modifications to turn NVIDIA runtime based jupyter image into CUDANN DEVEL base image.
# https://gitlab.com/nvidia/container-images/cuda/-/blob/master/dist/ubuntu18.04/10.1/devel/Dockerfile
# https://gitlab.com/nvidia/container-images/cuda/-/blob/master/dist/ubuntu18.04/10.1/devel/cudnn7/Dockerfile

# Contains some additional dependencies for Tensorflow and PyTorch:
#From https://github.com/tensorflow/tensorflow/blob/master/tensorflow/tools/dockerfiles/dockerfiles/gpu.Dockerfile
# https://github.com/pytorch/pytorch/blob/master/docker/pytorch/Dockerfile

USER root

ARG UBUNTU_VERSION=18.04

ENV ARCH=
ENV CUDA 10.1
ENV CUDNN 7.6.4.38-1
ENV CUDNN_MAJOR_VERSION=7
ENV LIB_DIR_PREFIX=x86_64
ENV LIBNVINFER=6.0.1-1
ENV LIBNVINFER_MAJOR_VERSION=6

LABEL com.nvidia.cudnn.version="${CUDNN}"

RUN apt-get update && apt-get install -y --no-install-recommends \
    build-essential \
    cuda-command-line-tools-$CUDA_PKG_VERSION \
    libcudnn7=$CUDNN+cuda10.1 \
    libcudnn7-dev=$CUDNN+cuda10.1 \
    cuda-nvrtc-$CUDA_PKG_VERSION \
    cuda-cufft-$CUDA_PKG_VERSION \
    cuda-curand-$CUDA_PKG_VERSION \
    cuda-cusolver-$CUDA_PKG_VERSION \
    cuda-cusparse-$CUDA_PKG_VERSION \
    cuda-libraries-dev-$CUDA_PKG_VERSION \
    cuda-minimal-build-$CUDA_PKG_VERSION \
    cuda-nvml-dev-$CUDA_PKG_VERSION \
    libcublas-dev=10.2.1.243-1 \
    libfreetype6-dev \
    libhdf5-serial-dev \
    libnccl-dev=$NCCL_VERSION-1+cuda$CUDA \
    libzmq3-dev \
    pkg-config \
    software-properties-common \
&& \
    apt-mark hold libcudnn7 && \
    rm -rf /var/lib/apt/lists/*

USER $NB_UID
