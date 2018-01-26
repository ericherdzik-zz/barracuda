####################
# BEGIN: Stage 0   #
####################

FROM ubuntu:16.04

# Build params
ARG ETHMINER_EXEC_URL
RUN test -n "${ETHMINER_EXEC_URL}"

RUN apt-get update && apt-get install -y --no-install-recommends \
    ca-certificates \
    wget

RUN wget --quiet -O ethminer.tar.gz ${ETHMINER_EXEC_URL}

####################
# END: Stage 0     #
####################

####################
# BEGIN: Stage 1   #
####################

FROM nvidia/cuda:9.0-base

COPY --from=0 ethminer.tar.gz .
RUN tar -zxf ethminer.tar.gz

COPY bin/start /bin/start
RUN chmod +x /bin/start

####################
# END: Stage 1     #
####################

# Run params, defaults
ENV ETHMINER_FARM_RECHECK 200
ENV ETHMINER_STRATUM us1.ethermine.org:4444
ENV ETHMINER_STRATUM_BACKUP us2.ethermine.org:4444
ENV GPU_FORCE_64BIT_PTR 0
ENV GPU_MAX_ALLOC_PERCENT 100
ENV GPU_MAX_HEAP_SIZE 100
ENV GPU_SINGLE_ALLOC_PERCENT 100
ENV GPU_USE_SYNC_OBJECTS 1

ENTRYPOINT [ "bin/start" ]
