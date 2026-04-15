ARG PYTORCH="1.9.0"
ARG CUDA="11.1"
ARG CUDNN="8"

FROM pytorch/pytorch:${PYTORCH}-cuda${CUDA}-cudnn${CUDNN}-devel

ENV TORCH_CUDA_ARCH_LIST="7.5 8.0 8.6 8.9"
ENV TORCH_NVCC_FLAGS="-Xfatbin -compress-all"
ENV CMAKE_PREFIX_PATH="$(dirname $(which conda))/../"

# To fix GPG key error when running apt-get update
RUN apt-key adv --fetch-keys https://developer.download.nvidia.com/compute/cuda/repos/ubuntu2404/x86_64/3bf863cc.pub
RUN apt-key adv --fetch-keys https://developer.download.nvidia.com/compute/machine-learning/repos/ubuntu2004/x86_64/7fa2af80.pub

RUN apt-get update && apt-get install -y git ninja-build libglib2.0-0 libsm6 libxrender-dev libxext6 libgl1-mesa-glx\
    && apt-get clean \
    && rm -rf /var/lib/apt/lists/*

# Install xtcocotools
RUN pip install cython
RUN pip install xtcocotools

# Install MMEngine and MMCV
#RUN pip install mmcv==2.2.0 -f https://download.openmmlab.com/mmcv/dist/cu121/torch2.4/index.html
RUN pip install openmim
RUN mim install mmengine "mmcv>=2.0.0rc4, <2.2.0"

# Install MMPose
RUN conda clean --all
RUN git clone https://github.com/mechaichiro/mmpose.git /mmpose
WORKDIR /mmpose
RUN git checkout main
ENV FORCE_CUDA="1"
RUN pip install -r requirements/build.txt
RUN pip install --no-cache-dir -e .

# Install runtime
WORKDIR /workspace
COPY ./requirements/runtime.txt /workspace/tmp/
RUN pip install -r /workspace/tmp/runtime.txt
COPY ./requirements/extends.txt /workspace/tmp/
RUN pip install -r /workspace/tmp/extends.txt
COPY ./requirements/mminstall.txt /workspace/tmp/
RUN mim install -r /workspace/tmp/mminstall.txt

WORKDIR /mmpose
