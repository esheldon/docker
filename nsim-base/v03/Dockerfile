# install packages needed for nsim
# nsim itself is not installed here

# we enter from galsim as user esheldon

FROM esheldon/galsim:v02

USER root

RUN pip install fitsio
RUN pip install esutil

RUN mkdir -p /build

WORKDIR /build

RUN git clone -b v0.9.3.1 https://github.com/esheldon/ngmix.git \
    && cd ngmix \
    && python setup.py install

RUN git clone -b v0.9.0 https://github.com/esheldon/covmatrix.git \
    && cd covmatrix \
    && python setup.py install

RUN git clone -b v0.1.0 https://github.com/esheldon/coaddsim.git \
    && cd coaddsim \
    && python setup.py install

RUN git clone -b v0.9.0 https://github.com/esheldon/navy.git \
    && cd navy \
    && python setup.py install

WORKDIR /root
RUN rm -r /build

USER esheldon
WORKDIR /home/esheldon
