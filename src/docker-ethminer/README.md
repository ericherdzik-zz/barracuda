# Docker Ethminer

The *unofficial* [Ethminer](https://github.com/ethereum-mining/ethminer) Docker image.

## How to use this image

This image was built and tested with Docker version `17.09.0-ce` and requires the [Docker Engine utility for NVIDIA GPUs](https://github.com/NVIDIA/nvidia-docker).

### Build

```bash
#!/usr/bin/env bash

sudo docker build -t ethminer .
```

Note: Be sure to build the Docker image within the `docker-ethminer` directory.

### Run

```bash
#!/usr/bin/env bash

sudo docker run \
    --runtime=nvidia \
    --env POOL="stratumss://0xA29d0014b84400d1fCF3480401Dc2A0251edd20B.default@us1.ethermine.org:5555" \
    ethminer
```

The following table enumerates all supported runtime parameters and their default values. Required parameters do not have defaults.

| Parameter | Required? | Default |
|-----------|-----------|---------|
|`POOL`|no|stratumss://0xA29d0014b84400d1fCF3480401Dc2A0251edd20B.default@us1.ethermine.org:5555|

## References

1. Docker Documentation. https://docs.docker.com
2. NVIDIA Docker Wiki. https://github.com/NVIDIA/nvidia-docker/wiki
3. Ethminer. https://github.com/ethereum-mining/ethminer
