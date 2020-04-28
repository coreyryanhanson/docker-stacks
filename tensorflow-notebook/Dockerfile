# Copyright (c) Jupyter Development Team.
# Distributed under the terms of the Modified BSD License.
# This is a fork of Jupyter's Dockerfile repository with minimal modifications to the base docker image.

ARG BASE_CONTAINER=coreyhanson/anacuda-scipy
FROM $BASE_CONTAINER

LABEL maintainer="Jupyter Project <jupyter@googlegroups.com>"

#Modifications for GPU Tensorflow
#From https://github.com/tensorflow/tensorflow/blob/master/tensorflow/tools/dockerfiles/dockerfiles/gpu.Dockerfile
# and https://gitlab.com/nvidia/container-images/cuda/-/blob/master/dist/ubuntu18.04/10.1/devel/cudnn7/Dockerfile

USER root

ARG UBUNTU_VERSION=18.04

ENV ARCH=
ENV CUDA 10.1
ENV CUDNN=7.6.4.38-1
ENV CUDNN_MAJOR_VERSION=7
ENV LIB_DIR_PREFIX=x86_64
ENV LIBNVINFER=6.0.1-1
ENV LIBNVINFER_MAJOR_VERSION=6

ENV CUDNN_VERSION 7.6.4.38-1
LABEL com.nvidia.cudnn.version="${CUDNN_VERSION}"

RUN apt-get update && apt-get install -y --no-install-recommends \
    build-essential \
    cuda-command-line-tools-$CUDA_PKG_VERSION \
    libcudnn7=$CUDNN_VERSION+cuda10.1 \
    cuda-nvrtc-$CUDA_PKG_VERSION \
    cuda-cufft-$CUDA_PKG_VERSION \
    cuda-curand-$CUDA_PKG_VERSION \
    cuda-cusolver-$CUDA_PKG_VERSION \
    cuda-cusparse-$CUDA_PKG_VERSION \
    libfreetype6-dev \
    libhdf5-serial-dev \
    libzmq3-dev \
    pkg-config \
    software-properties-common \
&& \
    apt-mark hold libcudnn7 && \
    rm -rf /var/lib/apt/lists/*

# Install TensorRT if not building for PowerPC
RUN [[ "${ARCH}" = "ppc64le" ]] || { apt-get update && \
        apt-get install -y --no-install-recommends libnvinfer${LIBNVINFER_MAJOR_VERSION}=${LIBNVINFER}+cuda$CUDA \
        libnvinfer-plugin${LIBNVINFER_MAJOR_VERSION}=${LIBNVINFER}+cuda$CUDA \
        && apt-get clean \
        && rm -rf /var/lib/apt/lists/*; }

# For CUDA profiling, TensorFlow requires CUPTI.
ENV LD_LIBRARY_PATH /usr/local/cuda/extras/CUPTI/lib64:/usr/local/cuda/lib64:$LD_LIBRARY_PATH

# Link the libcuda stub to the location where tensorflow is searching for it and reconfigure
# dynamic linker run-time bindings
RUN ln -s /usr/local/cuda/lib64/stubs/libcuda.so /usr/local/cuda/lib64/stubs/libcuda.so.1 \
    && echo "/usr/local/cuda/lib64/stubs" > /etc/ld.so.conf.d/z-cuda-stubs.conf \
    && ldconfig

COPY bashrc /etc/bash.bashrc
RUN chmod a+rwx /etc/bash.bashrc

USER $NB_UID

# See http://bugs.python.org/issue19846
ENV LANG C.UTF-8

RUN pip --no-cache-dir install --upgrade \
    setuptools && \
    fix-permissions $CONDA_DIR && \
    fix-permissions /home/$NB_USER

#From Jupyter Dockerfile
# Install Tensorflow
RUN pip install --quiet --no-cache-dir \
    'tensorflow' && \
    fix-permissions $CONDA_DIR && \
    fix-permissions /home/$NB_USER
