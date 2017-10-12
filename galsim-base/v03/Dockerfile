# get tmv and galsim installed, create esheldon user
FROM ubuntu:16.04

SHELL ["/bin/bash","-c"]

# some of this might be redundant
RUN apt-get update --fix-missing && apt-get install -y \
    curl grep sed dpkg \
	wget bzip2 ca-certificates \
	libglib2.0-0 libxext6 libsm6 libxrender1 \
    git \
	sudo build-essential \
    gcc g++ \
    fftw3-dev \
    libblas-dev

# set up tiny init
RUN TINI_VERSION=`curl https://github.com/krallin/tini/releases/latest | grep -o "/v.*\"" | sed 's:^..\(.*\).$:\1:'` && \
    curl -L "https://github.com/krallin/tini/releases/download/v${TINI_VERSION}/tini_${TINI_VERSION}.deb" > tini.deb && \
    dpkg -i tini.deb && \
    rm tini.deb && \
    apt-get clean

RUN mkdir /build/
WORKDIR /build

# at NERSC won't work unless we build ourselves

RUN wget http://www.mpich.org/static/downloads/3.2/mpich-3.2.tar.gz \
  && tar xvzf mpich-3.2.tar.gz \
  && cd mpich-3.2 \
  && ./configure \
  && make -j4 \
  && make install

RUN /sbin/ldconfig
RUN rm -rf /build/

#
# anaconda installed for user esheldon
#

# this creates a .bashrc
RUN useradd --create-home --shell /bin/bash esheldon
RUN adduser esheldon sudo

USER esheldon

ENV PATH /home/esheldon/miniconda2/bin:$PATH
ENV LD_LIBRARY_PATH /home/esheldon/local/lib:$LD_LIBRARY_PATH
ENV export LIBRARY_PATH /home/esheldon/local/lib:$LIBRARY_PATH
ENV C_INCLUDE_PATH /home/esheldon/local/include:$C_INCLUDE_PATH
ENV CPATH /home/esheldon/local/include:$CPATH

WORKDIR /home/esheldon

RUN mkdir -p local/src
RUN mkdir git

WORKDIR /home/esheldon/local/src

RUN wget --quiet https://repo.continuum.io/miniconda/Miniconda2-latest-Linux-x86_64.sh -O anaconda.sh && \
    /bin/bash anaconda.sh -b -p /home/esheldon/miniconda2 && \
    rm anaconda.sh

RUN source /home/esheldon/.bashrc

RUN echo "path: $PATH"

WORKDIR /home/esheldon/git

# install galsim requirements
# scipy only needed for some of the nosetests
RUN conda install -y astropy future boost pyyaml scons nose scipy

# TMV is required by galsim
RUN git clone https://github.com/rmjarvis/tmv.git \
    && cd tmv \
    && git checkout v0.74 \
    && scons WITH_OPENMP=False install PREFIX=/home/esheldon/local

# this is from the branch nostdstring
RUN git clone https://github.com/GalSim-developers/GalSim.git \
    && cd GalSim \
    && git checkout dd0d62c08d44421aa21b615ff7cb31704bcdd51c \
    && scons TMV_DIR=/home/esheldon/local install PREFIX=/home/esheldon/miniconda2 \
	&& cd tests \
	&& nosetests test*.py

RUN wget https://bitbucket.org/mpi4py/mpi4py/downloads/mpi4py-2.0.0.tar.gz \
  && tar xvzf mpi4py-2.0.0.tar.gz \
  && cd mpi4py-2.0.0 \
  && python setup.py install

ENTRYPOINT [ "/usr/bin/tini", "--" ]

CMD [ "/bin/bash" ]
