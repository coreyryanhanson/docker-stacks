# Copyright (c) Jupyter Development Team.
# Distributed under the terms of the Modified BSD License.
# This is a fork of Jupyter's Dockerfile repository with minimal modifications to the base docker image.

ARG BASE_CONTAINER=coreyhanson/anacuda-cuda-base
ARG BUILD_CONTAINER=coreyhanson/anacuda-cuda-devel
ARG PYTHON_VERSION=3.8
FROM $BUILD_CONTAINER as build

# Creates an installation of PyTorch and within a modified version of the official Jupyter notebook base image. Adapted from official and unofficial builds:
# https://github.com/pytorch/pytorch/blob/master/docker/pytorch/Dockerfile
# https://github.com/JaledMC/maskrcnn-benchmark/blob/fixDocker/docker/Dockerfile

USER $NB_UID

RUN source activate $CONDA_ROOT && \
    conda install --quiet --yes \
    'pyyaml' \
    'mkl' \
    'mkl-include' \
    'ninja' \
    'cython' \
    'typing' \
    'wheel' && \
    conda clean --all -f -y && \
    npm cache clean --force && \
    rm -rf /home/$NB_USER/.cache/yarn && \
    fix-permissions $CONDA_DIR && \
    fix-permissions /home/$NB_USER

RUN source activate $CONDA_ROOT && \
    mkdir /opt/conda/wheel-pytorch && \
    git clone https://github.com/pytorch/pytorch.git && \
    cd pytorch && \
    git submodule sync && git submodule update --init --recursive && \
    TORCH_CUDA_ARCH_LIST="3.5 5.2 6.0 6.1 7.0+PTX" TORCH_NVCC_FLAGS="-Xfatbin -compress-all" \
    CMAKE_PREFIX_PATH="$(dirname $(which conda))/../" \
    pip wheel . --wheel-dir=/opt/conda/wheel-pytorch && \
    cd .. && \
    rm -rf pytorch && \
    cd /opt/conda/wheel-pytorch && \
    pip install --no-index --find-links=/opt/conda/wheel-pytorch -r requirements.txt

RUN source activate $CONDA_ROOT && \
    mkdir /opt/conda/wheel-torchvision && \
    git clone https://github.com/pytorch/vision.git && \
    cd vision && \
    pip wheel . --wheel-dir=/opt/conda/wheel-torchvision && \
    cd /opt/conda/wheel-torchvision && \
    pip install --no-index --find-links=/opt/conda/wheel-torchvision -r requirements.txt



