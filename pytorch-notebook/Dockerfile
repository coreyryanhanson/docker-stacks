# Copyright (c) Jupyter Development Team.
# Distributed under the terms of the Modified BSD License.
# This is a fork of Jupyter's Dockerfile repository with minimal modifications to the base docker image.

ARG BASE_CONTAINER=coreyhanson/anacuda-cuda-base
FROM $BASE_CONTAINER

# Creates an installation of PyTorch and within a modified version of the official Jupyter notebook base image. Adapted from official and unofficial builds:
# https://github.com/pytorch/pytorch/blob/master/docker/pytorch/Dockerfile
# https://github.com/JaledMC/maskrcnn-benchmark/blob/fixDocker/docker/Dockerfile

USER $NB_UID

RUN source activate $CONDA_ROOT && \
    pip install --no-cache-dir torch==1.7.1+cu110 torchvision==0.8.2+cu110 torchaudio===0.7.2 -f https://download.pytorch.org/whl/torch_stable.html


