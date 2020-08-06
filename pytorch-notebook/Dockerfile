# Copyright (c) Jupyter Development Team.
# Distributed under the terms of the Modified BSD License.
# This is a fork of Jupyter's Dockerfile repository with minimal modifications to the base docker image.

ARG BASE_CONTAINER=coreyhanson/anacuda-devel-cudnn
FROM $BASE_CONTAINER

# Creates an installation of PyTorch and within a modified version of the official Jupyter notebook base image. Adapted from official and unofficial builds:
# https://github.com/pytorch/pytorch/blob/master/docker/pytorch/Dockerfile
# https://github.com/JaledMC/maskrcnn-benchmark/blob/fixDocker/docker/Dockerfile

USER $NB_UID

RUN conda install --quiet --yes \
    'ipython' \
    'pyyaml' \
    'ninja' \
    'cython' && \
    conda install --quiet --yes -c pytorch \
    'pytorch==1.3.0' \
    'torchvision==0.2.2' \
    cudatoolkit=${CUDA} && \
    conda clean --all -f -y && \
    jupyter lab build -y && \
    jupyter lab clean -y && \
    npm cache clean --force && \
    rm -rf /home/$NB_USER/.cache/yarn && \
    fix-permissions $CONDA_DIR && \
    fix-permissions /home/$NB_USER
