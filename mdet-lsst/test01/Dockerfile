# both these work outside docker
# preferred way
# mpiexec -n 1 -usize 4 mdet-lsst-sim-mpi --run run-drMWRIZ-e03-v1 --config run-drMWRIZ-e03-v1.yaml --njobs 3 --ntrial 1 --seed 31415
# also works
# mpiexec -n 4 python -m mpi4py.futures $(which mdet-lsst-sim-mpi) --run run-drMWRIZ-e03-v1 --config run-drMWRIZ-e03-v1.yaml --njobs 3 --ntrial 1 --seed 31415
#
#
# my attempts using docker
#
# this one with just calling mdet-lsst-sim-mpi directly
#
# mpiexec -n 1 -usize 4 docker run --mount type=bind,source="$(pwd)",target=/hostdata esheldon/mdet-lsst:test01 ./run.sh run-drMWRIZ-e03-v1 run-drMWRIZ-e03-v1.yaml 3 1 31415
#
#   mpiexec@59c76043aece] match_arg (utils/args/args.c:163): unrecognized argument pmi_args
#   [mpiexec@59c76043aece] HYDU_parse_array (utils/args/args.c:178): argument matching returned error
#   [mpiexec@59c76043aece] parse_args (ui/mpich/utils.c:1642): error parsing input array
#   [mpiexec@59c76043aece] HYD_uii_mpx_get_parameters (ui/mpich/utils.c:1694): unable to parse user arguments
#   [mpiexec@59c76043aece] main (ui/mpich/mpiexec.c:148): error parsing parameters
#
#
# now using python -m mpi4py.futures $(which mdet-lsst-sim-mpi)
#
# mpiexec -n 4 docker run --mount type=bind,source="$(pwd)",target=/hostdata esheldon/mdet-lsst:test01 ./run.sh run-drMWRIZ-e03-v1 run-drMWRIZ-e03-v1.yaml 3 1 31415
#
#   this the 3 jobs are all done on each core! Almost like
#   it is doing 3 parallel in each


FROM esheldon/mdet-lsst-base:v01

SHELL ["/bin/bash","-c"]

ARG STACKRC=/usr/local/share/stackvana-activate.sh
ARG RCFILE=/home/esheldon/.bashrc

# we need a user to run at nersc
# this creates a .bashrc
RUN useradd --create-home --shell /bin/bash esheldon
RUN mkdir -p /data &&  chown esheldon:esheldon /data
RUN mkdir -p /hostdata &&  chown esheldon:esheldon /hostdata

USER esheldon
SHELL ["/bin/bash","-c"]


RUN echo ". ${STACKRC}" > ${RCFILE}
#&& \
#    echo 'export PATH=/home/esheldon/.local/bin:$PATH' >> ${RCFILE}

ENV PATH="/home/esheldon/.local/bin:${PATH}"

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

#    echo 'python -m mpi4py.futures $(which mdet-lsst-sim-mpi) \' >> run.sh && \
#    echo 'mdet-lsst-sim-mpi \'       >> run.sh && \
RUN \
    echo '#!/bin/bash'                > run.sh && \
    echo ''                          >> run.sh && \
    echo ". ${RCFILE}"               >> run.sh && \
    echo 'export OMP_NUM_THREADS=1'  >> run.sh && \
    echo 'cd /hostdata'              >> run.sh && \
    echo ''                          >> run.sh && \
    echo 'run=$1'                    >> run.sh && \
    echo 'config=$2'                 >> run.sh && \
    echo 'njobs=$3'                  >> run.sh && \
    echo 'ntrial=$4'                 >> run.sh && \
    echo 'seed=$5'                   >> run.sh && \
    echo ''                          >> run.sh && \
    echo 'python -m mpi4py.futures $(which mdet-lsst-sim-mpi) \' >> run.sh && \
    echo '    --run ${run} \'        >> run.sh && \
    echo '    --config ${config} \'  >> run.sh && \
    echo '    --njobs ${njobs} \'    >> run.sh && \
    echo '    --ntrial ${ntrial} \'  >> run.sh && \
    echo '    --seed ${seed} '       >> run.sh && \
    chmod 755 run.sh
