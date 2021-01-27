# Copyright (c) Jupyter Development Team.
# Distributed under the terms of the Modified BSD License.
# This is a fork of Jupyter's Dockerfile repository with minimal modifications to the base docker image.
# CuPy is also added to this image at the end via pip which can leverage the GPU for Numpy calculations.

ARG BASE_CONTAINER=coreyhanson/anacuda-minimal
FROM $BASE_CONTAINER

LABEL maintainer="Jupyter Project <jupyter@googlegroups.com>"

USER root

# ffmpeg for matplotlib anim & dvipng+cm-super for latex labels
RUN apt-get update && \
    apt-get install -y --no-install-recommends ffmpeg dvipng cm-super && \
    apt-get clean && rm -rf /var/lib/apt/lists/*

USER $NB_UID

# Creates a root environment to install packages to for use within jupyter and better facilitate hardlinks between conda enviroments.
ENV CONDA_ROOT=main
RUN conda create -y --name $CONDA_ROOT python=$PYTHON_VERSION && \
    source activate $CONDA_ROOT && \
    conda config --env --prepend channels conda-forge && \
    conda config --env --set auto_update_conda false && \
    conda config --env --set show_channel_urls true && \
    conda config --env --set channel_priority strict && \
    conda update --all --quiet --yes && \
    conda clean --all -f -y && \
    rm -rf /home/$NB_USER/.cache/yarn && \
    fix-permissions $CONDA_DIR && \
    fix-permissions /home/$NB_USER

# Install Python 3 packages
RUN source activate $CONDA_ROOT && \
    conda install --quiet --yes \
    'beautifulsoup4=4.9.*' \
    'conda-forge::blas=*=openblas' \
    'bokeh=2.2.*' \
    'bottleneck=1.3.*' \
    'cloudpickle=1.6.*' \
    'cython=0.29.*' \
    'dask=2021.1.*' \
    'dill=0.3.*' \
    'h5py=3.1.*' \
    'ipywidgets=7.6.*' \
    'ipympl=0.6.*'\
    'matplotlib-base=3.3.*' \
    'numba=0.52.*' \
    'numexpr=2.7.*' \
    'pandas=1.2.*' \
    'patsy=0.5.*' \
    'protobuf=3.14.*' \
    'pytables=3.6.*' \
    'scikit-image=0.18.*' \
    'scikit-learn=0.24.*' \
    'scipy=1.6.*' \
    'seaborn=0.11.*' \
    'sqlalchemy=1.3.*' \
    'statsmodels=0.12.*' \
    'sympy=1.7.*' \
    'vincent=0.4.*' \
    'widgetsnbextension=3.5.*'\
    'xlrd=2.0.*' && \
    conda clean --all -f -y && \
    fix-permissions "${CONDA_DIR}" && \
    fix-permissions "/home/${NB_USER}"

# Install facets which does not have a pip or conda package at the moment
WORKDIR /tmp
RUN git clone https://github.com/PAIR-code/facets.git && \
    jupyter nbextension install facets/facets-dist/ --sys-prefix && \
    rm -rf /tmp/facets && \
    fix-permissions "${CONDA_DIR}" && \
    fix-permissions "/home/${NB_USER}"

# Import matplotlib the first time to build the font cache.
ENV XDG_CACHE_HOME="/home/${NB_USER}/.cache/"

RUN source activate $CONDA_ROOT && \
    MPLBACKEND=Agg python -c "import matplotlib.pyplot" && \
    fix-permissions "/home/${NB_USER}"

USER $NB_UID

WORKDIR $HOME

# Installs CuPy
RUN source activate $CONDA_ROOT && \
    pip install --no-cache-dir \
    'cupy-cuda101' && \
    fix-permissions $CONDA_DIR && \
    fix-permissions /home/$NB_USER

RUN jupyter kernelspec list

# Creates the base kernel for use in the Jupyter notebook.
RUN source activate $CONDA_ROOT && \
    python -m ipykernel install --prefix=/opt/conda/ --name $CONDA_ROOT --display-name "Python $PYTHON_VERSION ($CONDA_ROOT)" && \
    source activate base && \
    echo "y" | jupyter kernelspec remove python3 && \
    sed -i -e '$ac.KernelSpecManager.ensure_native_kernel = False' /etc/jupyter/jupyter_notebook_config.py
