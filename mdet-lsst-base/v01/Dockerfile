# inherit from stackvana, update the packages, and install both
# mpich and mpi4py

FROM beckermr/stackvana:latest

RUN \
    . /usr/local/share/stackvana-activate.sh && \
    conda update --all && \
    conda remove --force mpi mpich mpi4py

ARG mpich=3.3
ARG mpich_prefix=mpich-$mpich

RUN \
    . /usr/local/share/stackvana-activate.sh && \
    wget --quiet https://www.mpich.org/static/downloads/$mpich/${mpich_prefix}.tar.gz && \
    tar xzf ${mpich_prefix}.tar.gz                                          && \
    pushd ${mpich_prefix}                                                   && \
    ./configure --disable-fortran                                           && \
    make -j 4                                                               && \
    make install                                                            && \
    make clean                                                              && \
    popd                                                                    && \
    rm -rf ${mpich_prefix}

ARG mpi4py=3.0.0
ARG mpi4py_prefix=mpi4py-${mpi4py}

RUN \
    . /usr/local/share/stackvana-activate.sh && \
    wget --quiet https://bitbucket.org/mpi4py/mpi4py/downloads/${mpi4py_prefix}.tar.gz && \
    tar xvzf ${mpi4py_prefix}.tar.gz                                           && \
    pushd ${mpi4py_prefix}                                                     && \
    python3 setup.py build                                                     && \
    python3 setup.py install                                                   && \
    popd                                                                       && \
    rm -rf ${mpi4py_prefix}

RUN /sbin/ldconfig
