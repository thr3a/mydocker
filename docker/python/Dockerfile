FROM nvidia/cuda:10.0-cudnn7-runtime-ubuntu18.04

ENV MECAB_WORK=/tmp/mecab
ENV NEOLOGD_WORK=/tmp/neologd
ENV MECAB_DIC_PATH=/usr/lib/mecab/dic/neologd
ENV WORK_PATH=/workspace

RUN sed -i.bak -e "s%http://[^ ]\+%http://ftp.jaist.ac.jp/pub/Linux/ubuntu/%g" /etc/apt/sources.list && \
  apt-get update && apt-get install -y \
  python3 \
  python3-dev \
  python3-pip \
  build-essential \
  curl \
  vim \
  zip \
  unzip \
  git \
  file \
  swig \
  && rm -rf /var/lib/apt/lists/*

# # mecab
WORKDIR ${MECAB_WORK}
RUN git clone https://github.com/taku910/mecab.git ${MECAB_WORK} \
    && cd ./mecab && ./configure --enable-utf8-only && make -j$(nproc) && make install \
    && ldconfig && rm -rf ${MECAB_WORK}

# # neologd
WORKDIR ${NEOLOGD_WORK}
RUN mkdir -p ${MECAB_DIC_PATH} \
  && git clone --depth 1 https://github.com/neologd/mecab-ipadic-neologd.git ${NEOLOGD_WORK} \
  && ./bin/install-mecab-ipadic-neologd -n -y -p ${MECAB_DIC_PATH} && rm -rf ${NEOLOGD_WORK}

ADD ./mecabrc /usr/local/etc/mecabrc

RUN mkdir -p ${WORK_PATH}
WORKDIR ${WORK_PATH}

ADD ./requirements.txt /tmp/requirements.txt
RUN python3.6 -m pip install pip --upgrade && \
  pip3 install -r /tmp/requirements.txt

CMD ["jupyter-lab", "--no-browser", "--port=8888", "--ip=0.0.0.0", "--allow-root", "--NotebookApp.token=''" ]
