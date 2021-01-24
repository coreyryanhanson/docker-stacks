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

ARG UBUNTU_VERSION=20.04

ENV ARCH=
ENV CUDA 11.0
ENV CUDNN 8.0.5.39
ENV CUDNN_VERSION="${CUDNN}"
ENV CUDNN_MAJOR_VERSION=8
ENV LIB_DIR_PREFIX=x86_64
ENV LIBNVINFER=7.1.3-1
ENV LIBNVINFER_MAJOR_VERSION=7
ENV NCCL_VERSION 2.8.3

LABEL com.nvidia.cudnn.version="${CUDNN_VERSION}"

RUN apt-get update && apt-get install -y --no-install-recommends \
    build-essential \
    cuda-command-line-tools-11-0=11.0.3-1 \
    libcudnn8=$CUDNN_VERSION-1+cuda11.0 \
    libcudnn8-dev=$CUDNN_VERSION-1+cuda11.0 \
    cuda-nvrtc-${CUDA/./-} \
    cuda-cudart-dev-11-0=11.0.221-1 \
    libcufft-${CUDA/./-} \
    libcurand-${CUDA/./-} \
    libcusolver-${CUDA/./-} \
    libcusparse-${CUDA/./-} \
    cuda-libraries-dev-11-0=11.0.3-1 \
    cuda-minimal-build-11-0=11.0.3-1 \
    cuda-nvml-dev-11-0=11.0.167-1 \
    libcublas-dev-11-0=11.2.0.252-1 \
    libfreetype6-dev \
    libhdf5-serial-dev \
    libtinfo5 libncursesw5 \
    libnpp-dev-11-0=11.1.0.245-1 \
    libnccl-dev=2.8.3-1+cuda11.0 \
    libcusparse-11-0=11.1.1.245-1 \
    libcusparse-dev-11-0=11.1.1.245-1 \
    libzmq3-dev \
    pkg-config \
    software-properties-common \
&& \
    apt-mark hold libcublas-dev-11-0 libnccl-dev libcudnn8 && \
    rm -rf /var/lib/apt/lists/*

ENV LIBRARY_PATH /usr/local/cuda/lib64/stubs

USER $NB_UID
