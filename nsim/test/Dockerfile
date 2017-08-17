# install packages needed for nsim
# we enter here with user esheldon

FROM esheldon/nsim-base:v02

USER root

RUN mkdir -p /build
WORKDIR /build

RUN git clone https://github.com/esheldon/nsim.git \
    && cd nsim \
    && git checkout 23c7e4a92a3afce67e61aec1fa7921fae4c2db94 \
    && python setup.py install

USER esheldon
WORKDIR /home/esheldon

ENV NSIM_DIR /usr/local

# can override this by putting a command on the docker run
# command line
CMD nsim-run --seed=31415 run-e01-mcal-t02 100 /data/shapesim/run-e01-mcal-t02/outputs/bytrial/test.fits

