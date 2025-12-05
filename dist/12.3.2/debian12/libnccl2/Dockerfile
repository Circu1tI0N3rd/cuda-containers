ARG IMAGE_NAME
FROM ${IMAGE_NAME}:12.3.2-base-debian12 as base

ENV NV_CUDA_LIB_VERSION 12.3.2-1

FROM base as base-amd64

ENV NV_NVTX_VERSION 12.3.101-1

ENV NV_CUDA_CUDART_DEV_VERSION 12.3.101-1

ENV NV_LIBNCCL_PACKAGE_NAME libnccl2
ENV NV_LIBNCCL_PACKAGE_VERSION 2.20.3-1
ENV NCCL_VERSION 2.20.3-1
ENV NV_LIBNCCL_PACKAGE ${NV_LIBNCCL_PACKAGE_NAME}=${NV_LIBNCCL_PACKAGE_VERSION}+cuda12.3

FROM base as base-amd64

ENV NV_NVTX_VERSION 12.3.101-1

ENV NV_CUDA_CUDART_DEV_VERSION 12.3.101-1

ENV NV_LIBNCCL_PACKAGE_NAME libnccl2
ENV NV_LIBNCCL_PACKAGE_VERSION 2.20.3-1
ENV NCCL_VERSION 2.20.3-1
ENV NV_LIBNCCL_PACKAGE ${NV_LIBNCCL_PACKAGE_NAME}=${NV_LIBNCCL_PACKAGE_VERSION}+cuda12.3

FROM base-${TARGETARCH}

ARG TARGETARCH

LABEL maintainer "NVIDIA CORPORATION <cudatools@nvidia.com>"

ARG GENCODE

ENV NVCC_GENCODE=$GENCODE
ENV LIBRARY_PATH /usr/local/cuda/lib64/stubs
#
# install runtime and development then build nccl2
RUN apt-get update && apt-get install -y --no-install-recommends \
    cuda-libraries-12-3=${NV_CUDA_LIB_VERSION} \
    cuda-nvtx-12-3=${NV_NVTX_VERSION} \
    cuda-cudart-dev-12-3=${NV_CUDA_CUDART_DEV_VERSION} \
    cuda-command-line-tools-12-3=${NV_CUDA_LIB_VERSION} \
    cuda-minimal-build-12-3=${NV_CUDA_LIB_VERSION} \
    cuda-libraries-dev-12-3=${NV_CUDA_LIB_VERSION} \
    # extra build packages \
    wget python3 devscripts debhelper \
    # fetch and build nccl2 \
    && wget -O /usr/src/nccl2.tar.gz https://github.com/NVIDIA/nccl/archive/refs/tags/v${NCCL_VERSION}.tar.gz \
    && tar -C /usr/src -xzf /usr/src/nccl2.tar.gz; rm -f /usr/src/nccl2.tar.gz \
    && make -C /usr/src/nccl-${NCCL_VERSION} -j$((`nproc`/2)) src.build \
    && make -C /usr/src/nccl-${NCCL_VERSION} pkg.debian.build \
    # cleanup build deps \
    && apt-get autoremove -y \
    cuda-libraries-12-3=${NV_CUDA_LIB_VERSION} \
    cuda-nvtx-12-3=${NV_NVTX_VERSION} \
    cuda-cudart-dev-12-3=${NV_CUDA_CUDART_DEV_VERSION} \
    cuda-command-line-tools-12-3=${NV_CUDA_LIB_VERSION} \
    cuda-minimal-build-12-3=${NV_CUDA_LIB_VERSION} \
    cuda-libraries-dev-12-3=${NV_CUDA_LIB_VERSION} \
    # extra build packages \
    wget python3 devscripts debhelper \
    && rm -rf /var/lib/apt/lists/*
