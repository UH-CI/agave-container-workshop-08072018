# image: jstubbs/qiime

from ubuntu:16.04

RUN apt-get update -y
RUN apt-get install -y build-essential libsm6 libxext6 libgtk2.0-dev

RUN apt-get install -y git curl python3 vim wget tar bzip2 gzip lzma
RUN wget https://repo.continuum.io/miniconda/Miniconda3-latest-Linux-x86_64.sh

RUN bash Miniconda3-latest-Linux-x86_64.sh -b -p /opt/conda
RUN export PATH=/opt/conda/bin:$PATH && conda install -y conda && conda update -y conda

RUN mkdir /lus
RUN mkdir /lus/scratch
RUN /opt/conda/bin/conda create -n qiime1.9.1 python=2.7 qiime matplotlib=1.4.3  mock nose -c bioconda -y

ENTRYPOINT ["source", "activate", "qiime1.9.1"]
