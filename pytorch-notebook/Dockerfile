# Copyright (c) Jupyter Development Team.
# Distributed under the terms of the Modified BSD License.
# This is a fork of Jupyter's Dockerfile repository with minimal modifications to the base docker image.

ARG BASE_CONTAINER=coreyhanson/anacuda-scipy
FROM $BASE_CONTAINER

LABEL maintainer="Jupyter Project <jupyter@googlegroups.com>"

#Modifications to turn NVIDIA runtime based jupyter image into CUDANN DEVEL base image with pytorch installed. Dockerfiles used:
# https://github.com/pytorch/pytorch/blob/master/docker/pytorch/Dockerfile
# https://gitlab.com/nvidia/container-images/cuda/-/blob/master/dist/ubuntu18.04/10.1/devel/Dockerfile
# https://gitlab.com/nvidia/container-images/cuda/-/blob/master/dist/ubuntu18.04/10.1/devel/cudnn7/Dockerfile

USER root

#DEVEL
RUN apt-get update && apt-get install -y --no-install-recommends \
        cuda-nvml-dev-$CUDA_PKG_VERSION \
        cuda-command-line-tools-$CUDA_PKG_VERSION \
cuda-libraries-dev-$CUDA_PKG_VERSION \
        cuda-minimal-build-$CUDA_PKG_VERSION \
        libnccl-dev=$NCCL_VERSION-1+cuda10.1 \
libcublas-dev=10.2.1.243-1 \
&& \
    rm -rf /var/lib/apt/lists/*

ENV LIBRARY_PATH /usr/local/cuda/lib64/stubs

#CUDANN
ENV CUDNN_VERSION 7.6.5.32
LABEL com.nvidia.cudnn.version="${CUDNN_VERSION}"

RUN apt-get update && apt-get install -y --no-install-recommends \
    libcudnn7=$CUDNN_VERSION-1+cuda10.1 \
libcudnn7-dev=$CUDNN_VERSION-1+cuda10.1 \
&& \
    apt-mark hold libcudnn7 && \
    rm -rf /var/lib/apt/lists/*

# PYtorch install
ARG PYTHON_VERSION=3.7
ARG WITH_TORCHVISION=1
RUN apt-get update && apt-get install -y --no-install-recommends \
         cmake \
         curl \
         libjpeg-dev \
         libpng-dev && \
     rm -rf /var/lib/apt/lists/*
 
USER $NB_UID
#RUN conda config --show-sources
#RUN conda config --prepend channels defaults --strict-channel-priority

#RUN conda config --remove channels conda-forge
RUN conda search typing

RUN conda install --quiet --yes python=$PYTHON_VERSION  \
    'ipython' \
    'mkl-include' \
    'pyyaml' \
    'ninja' \
    'cython' && \
    conda update --all --quiet --yes && \
    conda clean --all -f -y && \
    jupyter lab build -y && \
    jupyter lab clean -y && \
    npm cache clean --force && \
    rm -rf /home/$NB_USER/.cache/yarn && \
    fix-permissions $CONDA_DIR && \
    fix-permissions /home/$NB_USER

RUN pip install --no-cache-dir \
    'mkl' \
    'typing'

RUN conda install --quiet --yes -c pytorch\
    'magma-cuda100' && \
    conda update --all --quiet --yes && \
    conda clean --all -f -y && \
    jupyter lab build -y && \
    jupyter lab clean -y && \
    npm cache clean --force && \
    rm -rf /home/$NB_USER/.cache/yarn && \
    fix-permissions $CONDA_DIR && \
    fix-permissions /home/$NB_USER


ENV PATH /opt/conda/bin:$PATH

RUN git clone --recursive https://github.com/pytorch/pytorch

WORKDIR $HOME/pytorch
RUN TORCH_CUDA_ARCH_LIST="3.5 5.2 6.0 6.1 7.0+PTX" TORCH_NVCC_FLAGS="-Xfatbin -compress-all" \
    CMAKE_PREFIX_PATH="$(dirname $(which conda))/../" \
    pip install -v .

RUN if [ "$WITH_TORCHVISION" = "1" ] ; then git clone https://github.com/pytorch/vision.git && cd vision && pip install -v . ; else echo "building without torchvision" ; fi

USER root

find / -name "workspace"

WORKDIR /workspace
RUN chmod -R a+w .

USER $NB_UID
WORKDIR $HOME
