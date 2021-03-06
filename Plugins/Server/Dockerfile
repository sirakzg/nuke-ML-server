FROM nvidia/cuda:10.0-cudnn7-devel-ubuntu18.04

ARG DEBIAN_FRONTEND=noninteractive
RUN apt-get update && apt-get install -y --no-install-recommends \
        build-essential \
        ca-certificates \
        cmake \
        curl \
        git \
        libglib2.0-0 \   
        libjpeg-dev \
        libopencv-dev \
        libpng-dev \
        libsm-dev \        
        vim && \
    rm -rf /var/lib/apt/lists/*

ARG PYTHON_VERSION=2.7
# Install conda with miniconda, install Python
RUN curl -o ~/miniconda.sh -O  https://repo.continuum.io/miniconda/Miniconda3-latest-Linux-x86_64.sh  && \
    chmod +x ~/miniconda.sh && \
    ~/miniconda.sh -b -p /opt/conda && \
    rm ~/miniconda.sh && \
    /opt/conda/bin/conda install -y python=$PYTHON_VERSION numpy pyyaml=3.13 scipy ipython mkl mkl-include cython typing
# Install PyTorch (which includes caffe2) and dependencies
RUN /opt/conda/bin/conda install -y magma-cuda100 -c pytorch && \
    /opt/conda/bin/conda install -y pytorch-nightly -c pytorch && \
    /opt/conda/bin/conda clean -ya

ENV PATH /opt/conda/bin:$PATH

RUN pip install --no-cache-dir --upgrade pip==9.0.3 setuptools wheel && \
    pip install --no-cache-dir \
    future \
    gast==0.2.2 \
    protobuf \
    scikit-image \
    tensorflow-determinism \
    tensorflow-gpu==1.14.0 \
    cupy==6.5.0 \
    imageio==2.6.1 \
    OpenEXR==1.3.2

WORKDIR /workspace
# Install the COCO API
RUN git clone https://github.com/cocodataset/cocoapi.git
RUN cd cocoapi/PythonAPI && make install
# Install detectron for mask RCNN
RUN git clone https://github.com/facebookresearch/detectron
RUN cd detectron && pip install -r requirements.txt && make
# Next line to find lib/libcaffe2_detectron_ops_gpu.so 
RUN cp /opt/conda/lib/python2.7/site-packages/torch/lib/libcaffe2_detectron_ops_gpu.so /usr/local/lib

WORKDIR /workspace/ml-server
# Copy your current folder to the docker image /workspace/ml-server/ folder
COPY . .