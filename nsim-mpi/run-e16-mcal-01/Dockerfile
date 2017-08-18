# install packages needed for nsim
# we enter here with user esheldon

FROM esheldon/nsim-base:v03

USER root

RUN mkdir -p /data/lensing/shapesim
RUN chown esheldon:esheldon /data/lensing/shapesim


RUN mkdir -p /build
WORKDIR /build

RUN git clone https://github.com/esheldon/nsim.git \
    && cd nsim \
    && git checkout 8a87631dc54690c19c160e9de372754f2d7eab90 \
    && python setup.py install


WORKDIR /root
RUN rm -r /build

USER esheldon
WORKDIR /home/esheldon

# the code that automatically determines the shared dir doesn't work
# since the executable is in /usr/bin but the share dir is in /usr/local
ENV NSIM_DIR /usr/local

# Define environment variable SHAPESIM_DIR; this will point to an externally
# mounted volume, e.g.
# /global/cscratch1/sd/esheldon/lensing/shapesim:/data/lensing/shapesim

ENV SHAPESIM_DIR /data/lensing/shapesim

# note using shifter at nersc, we must give the command, this one is ignored
#CMD nsim-run-mpi run-e01-mcal-t02
