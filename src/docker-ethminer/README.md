# Docker Ethminer

The *unofficial* [Ethminer](https://github.com/ethereum-mining/ethminer) Docker image.

## How to use this image

This image was built and tested with Docker version `17.09.0-ce` and requires the [Docker Engine utility for NVIDIA GPUs](https://github.com/NVIDIA/nvidia-docker).

### Build

```bash
#!/bin/bash

sudo docker build \
    --build-arg ETHMINER_URL=https://github.com/ethereum-mining/ethminer/releases/download/v0.16.1/ethminer-0.16.1-linux-x86_64.tar.gz \
    -t ethminer \
    .
```

#### Build Parameters

The following table enumerates all supported build parameters and their default values. Required parameters do not have defaults.

| Parameter | Required? | Default |
|-----------|-----------|---------|
|`ETHMINER_URL`|yes||

### Run

```bash
#!/bin/bash

sudo docker run \
    --env POOL="stratumss://0xA29d0014b84400d1fCF3480401Dc2A0251edd20B.example@us1.ethermine.org:5555"
    ethminer
```

The following table enumerates all supported runtime parameters and their default values. Required parameters do not have defaults.

| Parameter | Required? | Default |
|-----------|-----------|---------|
|`POOL`|no|stratumss://0xA29d0014b84400d1fCF3480401Dc2A0251edd20B.example@us1.ethermine.org:5555|

## References

1. Docker Documentation. https://docs.docker.com
2. NVIDIA Docker Wiki. https://github.com/NVIDIA/nvidia-docker/wiki
3. Ethminer. https://github.com/ethereum-mining/ethminer
