#  Copyright 2020 The Kale Authors
#
#  Licensed under the Apache License, Version 2.0 (the "License");
#  you may not use this file except in compliance with the License.
#  You may obtain a copy of the License at
#
#       http://www.apache.org/licenses/LICENSE-2.0
#
#  Unless required by applicable law or agreed to in writing, software
#  distributed under the License is distributed on an "AS IS" BASIS,
#  WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
#  See the License for the specific language governing permissions and
#  limitations under the License.

# Use tensorflow-1.14.0 as a base image, allowing the user to
# speficy if they want GPU support, by setting IMAGE_TYPE to "gpu".
ARG IMAGE_TYPE="gpu"
FROM gcr.io/kubeflow-images-public/tensorflow-2.1.0-notebook-gpu:1.0.0

USER root

ENV NB_PREFIX /

RUN apt-get -qq update
RUN DEBIAN_FRONTEND=noninteractive apt-get install -y --no-install-recommends apt-utils

# EOS
RUN apt-get -yqq install krb5-user libpam-krb5 && \
    apt-get -yqq clean && \
    mv /etc/krb5.conf /etc/krb5-backup.conf

# Install basic dependencies
RUN apt-get install -y --no-install-recommends \
        ca-certificates bash-completion tar less \
        python-pip python-setuptools build-essential python-dev \
        python3-pip python3-wheel && \
    rm -rf /var/lib/apt/lists/*

ENV SHELL /bin/bash
COPY bashrc /etc/bash.bashrc
COPY krb5.conf /etc/krb5.conf
RUN echo "set background=dark" >> /etc/vim/vimrc.local

# Install latest KFP SDK & Kale & JupyterLab Extension
RUN pip3 install --upgrade pip && \
    pip3 install --upgrade "jupyterlab<2.0.0" && \
    pip3 install https://storage.googleapis.com/ml-pipeline/release/latest/kfp.tar.gz --upgrade && \
    pip3 install -U kubeflow-kale && \
    jupyter labextension install kubeflow-kale-labextension

# Requirements
#COPY requirements/katib-example-requirements.txt /tmp/requirements.txt
#RUN pip3 install -r /tmp/requirements.txt

RUN echo "jovyan ALL=(ALL:ALL) NOPASSWD:ALL" > /etc/sudoers.d/jovyan

RUN ln -s /eos $HOME/eos
WORKDIR /home/jovyan
USER jovyan

EXPOSE 8888

CMD ["sh", "-c", \
     "jupyter lab --notebook-dir=/home/jovyan --ip=0.0.0.0 --no-browser \
      --allow-root --port=8888 --LabApp.token='' --LabApp.password='' \
      --LabApp.allow_origin='*' --LabApp.base_url=${NB_PREFIX}"]
