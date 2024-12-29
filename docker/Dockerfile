FROM nvcr.io/nvidia/pytorch:21.09-py3
ENV DEBIAN_FRONTEND=noninteractive 

# dependencies for gym
RUN apt-get update \
 && apt-get install -y --no-install-recommends \
 libxcursor-dev \
 libxrandr-dev \
 libxinerama-dev \
 libxi-dev \
 mesa-common-dev \
 zip \
 unzip \
 make \
 gcc-9 \
 g++-9 \
 vulkan-utils \
 mesa-vulkan-drivers \
 pigz \
 git \
 libegl1 \
 git-lfs

# Force gcc 8 to avoid CUDA 10 build issues on newer base OS
RUN update-alternatives --install /usr/bin/gcc gcc /usr/bin/gcc-9 9
RUN update-alternatives --install /usr/bin/g++ g++ /usr/bin/g++-9 9

# WAR for eglReleaseThread shutdown crash in libEGL_mesa.so.0 (ensure it's never detected/loaded)
# Can't remove package libegl-mesa0 directly (because of libegl1 which we need)
RUN rm /usr/lib/x86_64-linux-gnu/libEGL_mesa.so.0 /usr/lib/x86_64-linux-gnu/libEGL_mesa.so.0.0.0 /usr/share/glvnd/egl_vendor.d/50_mesa.json

COPY docker/nvidia_icd.json /usr/share/vulkan/icd.d/nvidia_icd.json
COPY docker/10_nvidia.json /usr/share/glvnd/egl_vendor.d/10_nvidia.json

WORKDIR /opt/isaacgym

RUN useradd --create-home gymuser
USER gymuser

# copy gym repo to docker
COPY --chown=gymuser . .

# install gym modules
ENV PATH="/home/gymuser/.local/bin:$PATH"
RUN cd python && pip install -e . \
 && git clone https://github.com/DanHrmti/ECRL.git 
RUN sed -i 's/isaacgym.egg==info/#isaacgym.egg==info/' python/ECRL/requirements.txt
RUN sed -i 's/scipy==1.12.0/scipy==1.6.3 #scipy==1.12.0/' python/ECRL/requirements.txt
RUN sed -i 's/numpy==1.24.3/numpy==1.21.2 #numpy==1.24.3/' python/ECRL/requirements.txt
RUN cd python/ECRL && pip install -r requirements.txt

ENV NVIDIA_VISIBLE_DEVICES=all NVIDIA_DRIVER_CAPABILITIES=all

