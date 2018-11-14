####################
# BEGIN: Stage 0   #
####################

FROM ubuntu:16.04

# Build params
ARG ETHMINER_URL
RUN test -n "${ETHMINER_URL}"

RUN apt-get update && apt-get install -y --no-install-recommends \
    ca-certificates \
    wget

RUN wget --quiet -O ethminer.tar.gz ${ETHMINER_URL}

####################
# END: Stage 0     #
####################

####################
# BEGIN: Stage 1   #
####################

FROM nvidia/cuda:10.0-base-ubuntu16.04

COPY --from=0 ethminer.tar.gz .
RUN tar -zxf ethminer.tar.gz

COPY bin/start /bin/start
RUN chmod +x /bin/start

####################
# END: Stage 1     #
####################

# Run params, defaults
ENV POOL=stratumss://0xA29d0014b84400d1fCF3480401Dc2A0251edd20B.default@us1.ethermine.org:5555

ENTRYPOINT [ "bin/start" ]
