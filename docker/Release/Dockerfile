FROM openmmlab/mmdeploy:ubuntu20.04-cuda11.3

ARG MMDEPLOY_VERSION

ENV BACKUP_LD_LIBRARY_PATH=$LD_LIBRARY_PATH
ENV LD_LIBRARY_PATH=/usr/local/cuda/compat:$LD_LIBRARY_PATH

RUN apt-get update \
    && apt-get install -y \
        python-is-python3 \
    && apt clean \
    && rm -rf /var/lib/apt/lists/* \
    && pip install -U pip

# build mmdeploy
RUN git clone --recursive -b $MMDEPLOY_VERSION --depth 1 https://github.com/open-mmlab/mmdeploy.git \
    && export Torch_DIR=$(python3 -c "import torch;print(torch.utils.cmake_prefix_path + '/Torch')") \
    && bash mmdeploy/.github/scripts/linux/build.sh "cpu;cuda" "ort;trt;ncnn;torchscript;openvino" \
        -DMMDEPLOY_BUILD_EXAMPLES=ON \
        -Dpplcv_DIR=${pplcv_DIR} \
        -DTENSORRT_DIR=${TENSORRT_DIR} \
        -DInferenceEngine_DIR=${InferenceEngine_DIR} \
        -DONNXRUNTIME_DIR=${ONNXRUNTIME_DIR} \
        -Dncnn_DIR=${ncnn_DIR} \
        -DTorch_DIR=${Torch_DIR} \
    && cd mmdeploy \
    && python -m pip install -U openmim pycuda \
    && python -m mim install mmcv==2.0.1 \
    && python -m pip install -r requirements.txt \
    && python -m pip install -e .

ENV MMDeploy_DIR="/root/workspace/mmdeploy/build/install/lib/cmake/MMDeploy"
ENV LD_LIBRARY_PATH="/root/workspace/mmdeploy/build/lib:${BACKUP_LD_LIBRARY_PATH}"
ENV PATH="/root/workspace/mmdeploy/build/bin:${PATH}"
ENV PYTHONPATH="/root/workspace/mmdeploy/build/lib:${PYTHONPATH}"
