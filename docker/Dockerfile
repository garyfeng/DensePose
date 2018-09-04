# Use Caffe2 image as parent image
FROM caffe2/caffe2:snapshot-py2-cuda9.0-cudnn7-ubuntu16.04

RUN mv /usr/local/caffe2 /usr/local/caffe2_build
ENV Caffe2_DIR /usr/local/caffe2_build

ENV PYTHONPATH /usr/local/caffe2_build:${PYTHONPATH}
ENV LD_LIBRARY_PATH /usr/local/caffe2_build/lib:${LD_LIBRARY_PATH}

# Clone the Detectron repository
RUN git clone https://github.com/facebookresearch/densepose /densepose

# Install Python dependencies
RUN pip install -r /densepose/requirements.txt

# Install the COCO API
RUN git clone https://github.com/cocodataset/cocoapi.git /cocoapi
WORKDIR /cocoapi/PythonAPI
RUN make install

# Go to Densepose root
WORKDIR /densepose

# Set up Python modules
RUN make

# [Optional] Build custom ops
RUN make ops

# changes by garyfeng:
RUN apt update && apt install wget

# Fetch DensePose data.
RUN bash /densepose/DensePoseData/get_densepose_uv.sh
RUN mv UV_data /densepose/DensePoseData/

# For training, download the DensePose-COCO dataset:
RUN bash /densepose/DensePoseData/get_DensePose_COCO.sh
RUN mv DensePose_COCO /densepose/DensePoseData/

# For evaluation, get the necessary files:
RUN bash /densepose/DensePoseData/get_eval_data.sh
RUN mv eval_data /densepose/DensePoseData/

# Setting-up the COCO dataset.
# download it from http://cocodataset.org/#download
#    Note that the total size of COCO is ~100G
#    follow the instructions therein to actually download
#   wget http://images.cocodataset.org/zips/train2014.zip
#   wget http://images.cocodataset.org/zips/val2014.zip
#   wget http://images.cocodataset.org/annotations/annotations_trainval2014.zip
# and set up in the host directory something like the following directory:
# coco
# |_ coco_train2014
# |  |_ <im-1-name>.jpg
# |  |_ ...
# |  |_ <im-N-name>.jpg
# |_ coco_val2014
# |_ ...
# |_ annotations
#   |_ instances_train2014.json
#   |_ ...

# Create a symlink for the COCO dataset in your datasets/data folder.
#    RUN ln -s /path/to/coco /densepose/detectron/datasets/data/coco

# Create symlinks for the DensePose-COCO annotations
# RUN ln -s /densepose/DensePoseData/DensePose_COCO/densepose_coco_2014_minival.json /densepose/detectron/datasets/data/coco/annotations/
# RUN ln -s /densepose/DensePoseData/DensePose_COCO/densepose_coco_2014_train.json /densepose/detectron/datasets/data/coco/annotations/
# RUN ln -s /densepose/DensePoseData/DensePose_COCO/densepose_coco_2014_valminusminival.json /densepose/detectron/datasets/data/coco/annotations/
