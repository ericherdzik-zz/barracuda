# Docker Ethminer

The *unofficial* [Ethminer](https://github.com/ethereum-mining/ethminer) Docker image.

## How to use this image

This image was built and tested with Docker version `17.09.0-ce` and requires the [Docker Engine utility for NVIDIA GPUs](https://github.com/NVIDIA/nvidia-docker).

### Build

```bash
#!/bin/bash

sudo docker build \
    --build-arg ETHMINER_EXEC_URL=https://github.com/ethereum-mining/ethminer/releases/download/v0.12.0/ethminer-0.12.0-Linux.tar.gz \
    -t ethminer \
    .
```

#### Build Parameters

The following table enumerates all supported build parameters and their default values. Required parameters do not have defaults.

| Parameter | Required? | Default |
|-----------|-----------|---------|
|`ETHMINER_EXEC_URL`|yes||

### Run

```bash
#!/bin/bash

sudo docker run \
    --env ETHMINER_STRATUM_CREDENTIALS=0xA29d0014b84400d1fCF3480401Dc2A0251edd20B.example \
    --runtime=nvidia \
    ethminer
```

The following table enumerates all supported runtime parameters and their default values. Required parameters do not have defaults.

| Parameter | Required? | Default |
|-----------|-----------|---------|
|`ETHMINER_FARM_RECHECK`|no|200|
|`ETHMINER_STRATUM`|no|us1.ethermine.org:4444|
|`ETHMINER_STRATUM_BACKUP`|no|us2.ethermine.org:4444|
|`ETHMINER_STRATUM_CREDENTIALS`|yes||
|`GPU_FORCE_64BIT_PTR`|no|0|
|`GPU_MAX_ALLOC_PERCENT`|no|100|
|`GPU_MAX_HEAP_SIZE`|no|100|
|`GPU_SINGLE_ALLOC_PERCENT`|no|100|
|`GPU_USE_SYNC_OBJECTS`|no|1|

## References

1. Docker Documentation. https://docs.docker.com
2. NVIDIA Docker Wiki. https://github.com/NVIDIA/nvidia-docker/wiki
3. Ethminer. https://github.com/ethereum-mining/ethminer
