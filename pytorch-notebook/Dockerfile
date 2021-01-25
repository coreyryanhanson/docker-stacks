# Copyright (c) Jupyter Development Team.
# Distributed under the terms of the Modified BSD License.
# This is a fork of Jupyter's Dockerfile repository with minimal modifications to the base docker image.

ARG BASE_CONTAINER=coreyhanson/anacuda-devel-cudnn
FROM $BASE_CONTAINER as first

# Creates an installation of PyTorch and within a modified version of the official Jupyter notebook base image. Adapted from official and unofficial builds:
# https://github.com/pytorch/pytorch/blob/master/docker/pytorch/Dockerfile
# https://github.com/JaledMC/maskrcnn-benchmark/blob/fixDocker/docker/Dockerfile

USER root
ARG PYTHON_VERSION=3.8
ARG WITH_TORCHVISION=1
RUN apt-get update && apt-get install -y --no-install-recommends \
         build-essential \
         cmake \
         git \
         curl \
         ca-certificates \
         libjpeg-dev \
         libpng-dev && \
     rm -rf /var/lib/apt/lists/*

USER $NB_UID
  
RUN source activate $CONDA_ROOT && \
    conda install --quiet --yes \
    'pyyaml' \
    'mkl' \
    'mkl-include' \
    'ninja' \
    'cython' \
    'typing' && \
    conda clean --all -f -y && \
    npm cache clean --force && \
    rm -rf /home/$NB_USER/.cache/yarn && \
    fix-permissions $CONDA_DIR && \
    fix-permissions /home/$NB_USER

FROM first as build

RUN source activate $CONDA_ROOT && \
    git clone https://github.com/pytorch/pytorch.git && \
    cd pytorch && \
    git submodule sync && git submodule update --init --recursive && \
    TORCH_CUDA_ARCH_LIST="3.5 5.2 6.0 6.1 7.0+PTX" TORCH_NVCC_FLAGS="-Xfatbin -compress-all" \
    CMAKE_PREFIX_PATH="$(dirname $(which conda))/../" \
    pip install -v .

RUN if [ "$WITH_TORCHVISION" = "1" ] ; then git clone https://github.com/pytorch/vision.git && cd vision && pip install -v . ; else echo "building without torchvision" ; fi



