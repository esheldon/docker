# example uses at nersc
# shifterimg -v pull esheldon/mdet-lsst:{run-name}
#
#!/bin/bash
#SBATCH --account=m1727
#SBATCH --mail-type=begin,end,fail
#SBATCH --mail-user=esheldon@bnl.gov
#SBATCH --image=esheldon/mdet-lsst:test01
#SBATCH --qos=debug
#SBATCH --constraint=knl
#SBATCH --nodes=1
#SBATCH --time=30
#
# run=run-drMWRIZ-e03-v1
# config=${run}.yaml
# njobs=67
# ntrials=5
# seed=31415
#
# srun -n 68 \
#     shifter \
#     /home/esheldon/run.sh \
#     ${run} \
#     ${config} \
#     ${njobs} \
#     ${ntrials} \
#     ${seed}


FROM esheldon/mdet-lsst-base:v01

SHELL ["/bin/bash","-c"]

ARG STACKRC=/usr/local/share/stackvana-activate.sh
ARG RCFILE=/home/esheldon/.bashrc

# we need a user to run at nersc
# this creates a .bashrc
RUN useradd --create-home --shell /bin/bash esheldon
RUN mkdir -p /data &&  chown esheldon:esheldon /data
# RUN mkdir -p /hostdata &&  chown esheldon:esheldon /hostdata

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
    pip install --no-deps git+git://github.com/LSSTDESC/descwl-shear-sims.git@0f9abcd8d13152cca5277e1c64a2ddb4200bd718 && \
    pip install --no-deps git+git://github.com/LSSTDESC/descwl_coadd.git@6f89a8aa4a5484a1573e1c389d50974c6a9099e3 && \
    pip install --no-deps git+git://github.com/esheldon/metadetect.git@a63236f063fe2979c7fbf89563080e6fe66895fb && \
    pip install --no-deps git+git://github.com/esheldon/mdet-lsst-sim.git@c05503088e414179168e2a120cab383557af6cb0

#    echo 'cd /hostdata'               >> run.sh && \
#    echo 'python -m mpi4py.futures $(which mdet-lsst-sim-mpi) \' >> run.sh && \
#    echo 'mdet-lsst-sim-mpi \'       >> run.sh && \
RUN \
    echo '#!/bin/bash'                 > run.sh && \
    echo ''                           >> run.sh && \
    echo 'export HOME=/home/esheldon' >> run.sh && \
    echo ''                           >> run.sh && \
    echo ". ${RCFILE}"                >> run.sh && \
    echo 'export OMP_NUM_THREADS=1'   >> run.sh && \
    echo ''                           >> run.sh && \
    echo 'run=$1'                     >> run.sh && \
    echo 'config=$2'                  >> run.sh && \
    echo 'njobs=$3'                   >> run.sh && \
    echo 'ntrial=$4'                  >> run.sh && \
    echo 'seed=$5'                    >> run.sh && \
    echo ''                           >> run.sh && \
    echo 'python -m mpi4py.futures $(which mdet-lsst-sim-mpi) \' >> run.sh && \
    echo '    --run ${run} \'         >> run.sh && \
    echo '    --config ${config} \'   >> run.sh && \
    echo '    --njobs ${njobs} \'     >> run.sh && \
    echo '    --ntrial ${ntrial} \'   >> run.sh && \
    echo '    --seed ${seed} '        >> run.sh && \
    chmod 755 run.sh
