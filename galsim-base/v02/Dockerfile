# get tmv and galsim installed, create esheldon user
FROM ubuntu:16.04

SHELL ["/bin/bash","-c"]

# some of this might be redundant
RUN apt-get update --fix-missing && apt-get install -y \
    curl grep sed dpkg \
	wget bzip2 ca-certificates \
	libglib2.0-0 libxext6 libsm6 libxrender1 \
    git mercurial subversion \
	sudo build-essential scons \
    gcc g++ \
    gfortran \
    fftw3-dev \
    python-dev \
    python-scipy \
    python-astropy \
    python-pip \
    python-future \
    python-yaml \
    python-sklearn \
    python-statsmodels \
    libboost-python-dev \
    libblas-dev

# set up tiny init
RUN TINI_VERSION=`curl https://github.com/krallin/tini/releases/latest | grep -o "/v.*\"" | sed 's:^..\(.*\).$:\1:'` && \
    curl -L "https://github.com/krallin/tini/releases/download/v${TINI_VERSION}/tini_${TINI_VERSION}.deb" > tini.deb && \
    dpkg -i tini.deb && \
    rm tini.deb && \
    apt-get clean

RUN mkdir /build/
WORKDIR /build

# NERSC says won't work unless we build these ourselves

RUN wget http://www.mpich.org/static/downloads/3.2/mpich-3.2.tar.gz \
  && tar xvzf mpich-3.2.tar.gz \
  && cd mpich-3.2 \
  && ./configure \
  && make -j4 \
  && make install

RUN wget https://bitbucket.org/mpi4py/mpi4py/downloads/mpi4py-2.0.0.tar.gz \
  && tar xvzf mpi4py-2.0.0.tar.gz \
  && cd mpi4py-2.0.0 \
  && python setup.py install

RUN git clone https://github.com/rmjarvis/tmv.git \
    && cd tmv \
    && git checkout v0.74 \
    && scons WITH_OPENMP=False install PREFIX=/usr/local

RUN git clone https://github.com/GalSim-developers/GalSim.git \
    && cd GalSim \
    && git checkout ab8522247101536017f5e46a69852d3c2eb775e2 \
    && scons TMV_DIR=/usr/local install

RUN rm -rf /build/

RUN /sbin/ldconfig

# this creates a .bashrc
RUN useradd --create-home --shell /bin/bash esheldon
RUN adduser esheldon sudo

USER esheldon
WORKDIR /home/esheldon

RUN mkdir local
RUN mkdir git

ENTRYPOINT [ "/usr/bin/tini", "--" ]

CMD [ "/bin/bash" ]
