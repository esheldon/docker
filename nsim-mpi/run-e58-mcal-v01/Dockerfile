# install packages needed for nsim
# we enter here with user esheldon

FROM esheldon/nsim-base:v06

WORKDIR /home/esheldon/git

RUN git clone https://github.com/esheldon/nsim.git \
    && cd nsim \
    && git checkout b9b9bee1d779b96c3a00172f7632f78a55d6d347 \
    && python setup.py install

WORKDIR /home/esheldon

# Define environment variable SHAPESIM_DIR; this will point to an externally
# mounted volume, e.g.
# /global/cscratch1/sd/esheldon/lensing/shapesim:/data/lensing/shapesim

ENV SHAPESIM_DIR /data/lensing/shapesim

# note using shifter at nersc, we must give the command, this one is ignored
#CMD nsim-run-mpi run-em01-mcal-02
