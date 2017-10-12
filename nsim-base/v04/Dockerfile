# install packages needed for nsim
# nsim itself is not installed here, rather
# the nsim docker used for work should use
# a FROM statement to base off this one

# we enter from galsim as user esheldon

# remember to change when galsim version finalized
FROM esheldon/galsim:v03

RUN conda install numba
RUN pip install fitsio
RUN pip install esutil

WORKDIR /home/esheldon/git

#RUN git clone -b v0.9.3.1 https://github.com/esheldon/ngmix.git \
RUN git clone https://github.com/esheldon/ngmix.git \
    && cd ngmix \
    && git checkout 22b4137e9b5601fca89095d5876468dfea66134a \
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

RUN python -c 'import ngmix; ngmix.test.test()'

WORKDIR /home/esheldon
