FROM esheldon/mdet-lsst-base:v01

SHELL ["/bin/bash","-c"]

# we need a user to run at nersc
# this creates a .bashrc
RUN useradd --create-home --shell /bin/bash esheldon
RUN mkdir -p /data &&  chown esheldon:esheldon /data

USER esheldon
SHELL ["/bin/bash","-c"]

ARG STACKRC=/usr/local/share/stackvana-activate.sh
ARG RCFILE=/home/esheldon/.bashrc

RUN echo ". ${STACKRC}" > ${RCFILE} && \
    echo 'export PATH=/home/esheldon/.local/bin:$PATH' >> ${RCFILE}

RUN pushd /data && \
    wget --quiet https://www.cosmo.bnl.gov/www/esheldon/data/catsim.tar.gz && \
    tar xvfz catsim.tar.gz

ENV CATSIM_DIR /data/catsim
WORKDIR /home/esheldon

RUN \ 
    . ${STACKRC} && \
    pip install --no-deps git+git://github.com/LSSTDESC/descwl-shear-sims.git@aaf8a0c37a045e64b4b4f42f9b4d2950014da455 && \
    pip install --no-deps git+git://github.com/LSSTDESC/descwl_coadd.git@a4a86e9a6fe365b2a864b2af183e110da9038edf && \
    pip install --no-deps git+git://github.com/esheldon/metadetect.git@a63236f063fe2979c7fbf89563080e6fe66895fb && \
    pip install --no-deps git+git://github.com/esheldon/mdet-lsst-sim.git@c1c2df8c4f5916bbc36989ab5aa5716baae38f31


#    echo '. /usr/local/share/stackvana-activate.sh' >> test.sh && \
RUN \
    echo '#!/bin/bash' > test.sh && \
    echo ". ${RCFILE}" >> test.sh && \
    echo 'mdet-lsst-sim' >> test.sh && \
    chmod 755 test.sh
