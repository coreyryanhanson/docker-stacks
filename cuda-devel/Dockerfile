# Copyright (c) Jupyter Development Team.
# Distributed under the terms of the Modified BSD License.
# This is a fork of Jupyter's Dockerfile repository with minimal modifications to the base docker image.

ARG BASE_CONTAINER=coreyhanson/anacuda-cuda-base
FROM $BASE_CONTAINER

# Modifications to turn NVIDIA runtime based jupyter image into CUDANN DEVEL base image.
# https://gitlab.com/nvidia/container-images/cuda/-/blob/master/dist/ubuntu18.04/10.1/devel/Dockerfile
# https://gitlab.com/nvidia/container-images/cuda/-/blob/master/dist/ubuntu18.04/10.1/devel/cudnn7/Dockerfile

# Contains some additional dependencies for Tensorflow and PyTorch:
#From https://github.com/tensorflow/tensorflow/blob/master/tensorflow/tools/dockerfiles/dockerfiles/gpu.Dockerfile
# https://github.com/pytorch/pytorch/blob/master/docker/pytorch/Dockerfile

USER root

ARG UBUNTU_VERSION=18.04

LABEL com.nvidia.cudnn.version="${CUDNN_VERSION}"

RUN apt-get update && apt-get install -y --no-install-recommends \
    libcudnn8-dev=$CUDNN_VERSION-1+cuda11.0 \
    cuda-cudart-dev-11-0=11.0.221-1 \
    cuda-libraries-dev-11-0=11.0.3-1 \
    cuda-minimal-build-11-0=11.0.3-1 \
    cuda-nvml-dev-11-0=11.0.167-1 \
    libcublas-dev-11-0=11.2.0.252-1 \
    libtinfo5 libncursesw5 \
    libnpp-dev-11-0=11.1.0.245-1 \
    libnccl-dev=2.8.3-1+cuda11.0 \
    libcusparse-dev-11-0=11.1.1.245-1 \
    libjpeg-dev \
    libpng-dev \
&& \
    rm -rf /var/lib/apt/lists/*

ENV LIBRARY_PATH /usr/local/cuda/lib64/stubs

USER $NB_UID
