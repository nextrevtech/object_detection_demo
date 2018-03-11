FROM "ubuntu"
RUN apt-get update 
RUN mkdir -p /tensorflow/models
RUN apt-get install -y git python-pip wget curl
RUN easy_install -U pip
RUN pip install tensorflow
RUN apt-get install -y protobuf-compiler python-pil python-lxml python-tk
RUN pip install jupyter
RUN pip install matplotlib
RUN git clone https://github.com/tensorflow/models.git /tensorflow/models
WORKDIR /tensorflow/models/research
RUN protoc object_detection/protos/*.proto --python_out=.
RUN export PYTHONPATH=$PYTHONPATH:`pwd`:`pwd`/slim
RUN wget http://host.robots.ox.ac.uk/pascal/VOC/voc2012/VOCtrainval_11-May-2012.tar
RUN tar -xvf VOCtrainval_11-May-2012.tar
CMD python object_detection/dataset_tools/create_pascal_tf_record.py --label_map_path=object_detection/data/pascal_label_map.pbtxt --data_dir=VOCdevkit --year=VOC2012 --set=train --output_path=pascal_train.record
CMD python object_detection/dataset_tools/create_pascal_tf_record.py --label_map_path=object_detection/data/pascal_label_map.pbtxt --data_dir=VOCdevkit --year=VOC2012 --set=val --output_path=pascal_val.record
RUN wget http://www.robots.ox.ac.uk/~vgg/data/pets/data/images.tar.gz
RUN wget http://www.robots.ox.ac.uk/~vgg/data/pets/data/annotations.tar.gz
RUN tar -xvf annotations.tar.gz
RUN tar -xvf images.tar.gz
ENV PYTHONPATH /tensorflow/models/research/object_detection
RUN PYTHONPATH=/tensorflow/models/research/ python object_detection/dataset_tools/create_pet_tf_record.py --label_map_path=object_detection/data/pet_label_map.pbtxt --data_dir=`pwd` --output_dir=`pwd`
RUN mkdir -p /tensorflow/models/research/object_detection/models/models/train
RUN jupyter notebook --generate-config --allow-root
RUN echo "c.NotebookApp.password = u'sha1:6a3f528eec40:6e896b6e4828f525a6e20e5411cd1c8075d68619'" >> /root/.jupyter/jupyter_notebook_config.py
RUN echo "export PYTHONPATH=$PYTHONPATH:/tensorflow/models/research:/tensorflow/models/research/slim" >> /root/.bashrc
EXPOSE 8888
CMD ["jupyter", "notebook", "--allow-root", "--notebook-dir=/tensorflow/models/research/object_detection", "--ip='*'", "--port=8888", "--no-browser"]
