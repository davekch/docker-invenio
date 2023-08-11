#
# Copyright (C) 2018-2021 CERN.
# Copyright (C) 2022 Graz University of Technology.
# Copyright (C) 2022 University of Münster.
#
# Invenio is free software; you can redistribute it and/or modify it
# under the terms of the MIT License; see LICENSE file for more details.
#
ARG LINUX_VERSION=22.04
ARG BUILDPLATFORM=linux/amd64
FROM --platform=$BUILDPLATFORM ubuntu:${LINUX_VERSION}

ARG PYTHON=python3.9

RUN apt update && apt upgrade -y && \
    apt install -y \
        git \
        wget \
        build-essential \
        software-properties-common \
        locales locales-all

RUN localedef -i en_US -c -f UTF-8 -A /usr/share/locale/locale.alias en_US.UTF-8

ENV LANG en_US.UTF-8
ENV LANGUAGE en_US:en
ENV LC_ALL en_US.UTF-8
ENV TZ=Europe/Berlin
RUN ln -snf /usr/share/zoneinfo/$TZ /etc/localtime && echo $TZ > /etc/timezone

RUN add-apt-repository ppa:deadsnakes/ppa -y
RUN apt update && apt install -y \
        ${PYTHON} \ 
        ${PYTHON}-dev \
        ${PYTHON}-distutils \
        libcairo2-dev \
        fonts-dejavu \
        libffi-dev \
        libpq-dev \
        libxml2-dev \
        libxslt-dev \
        imagemagick \
        libssl-dev \
        libbz2-dev \
        xz-utils \
        libsqlite3-dev \
        libxmlsec1-dev


# `python3-packaging` is installed and it causes issues with `pip` installations
RUN apt remove python3-packaging -y

# Symlink Python
RUN ln -sfn /usr/bin/${PYTHON} /usr/bin/python

# install pip for ${PYTHON}
RUN wget https://bootstrap.pypa.io/get-pip.py -O get-pip.py && \
    python get-pip.py
RUN python -m pip install --upgrade pip pipenv wheel setuptools

# Install Node.js v16
RUN curl -fsSL https://rpm.nodesource.com/setup_16.x | bash - && \
    apt install -y nodejs

# Create working directory
ENV WORKING_DIR=/opt/invenio
ENV INVENIO_INSTANCE_PATH=${WORKING_DIR}/var/instance

# Create files mountpoints
RUN mkdir -p ${INVENIO_INSTANCE_PATH} && \
    mkdir ${INVENIO_INSTANCE_PATH}/data ${INVENIO_INSTANCE_PATH}/archive ${INVENIO_INSTANCE_PATH}/static

# copy everything inside /src
RUN mkdir -p ${WORKING_DIR}/src
WORKDIR ${WORKING_DIR}/src

# Set folder permissions
ENV INVENIO_USER_ID=1000
RUN chgrp -R 0 ${WORKING_DIR} && \
    chmod -R g=u ${WORKING_DIR}
RUN useradd invenio --uid ${INVENIO_USER_ID} --gid 0 && \
    chown -R invenio:root ${WORKING_DIR}
