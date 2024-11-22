# bazeld

**This is not an official Google product.**

Bazeld is a thin wrapper around bazel that launches a persistent docker
container and runs bazel in the container.

Benefits of bazeld:

* Filesystem is automatically mounted for
  * Repository root
  * Bazel root (and cache)
* Handles docker in docker
* Automatically picks up GCP auth credentials
* Can run as root or your current USER (see [Configuration](#configuration)).
* On every execution, reuses the container from prior runs which enables
  re-using local bazel service and cache.
* On every execution, automatically determines if a newer version of the target
  docker image is available and restarts the container if so.

# Installation

Copy the `bazeld` script into your `~/bin` or `/usr/bin/` location. Make sure to 
`chmod +x ~/bin/bazeld || sudo chmod +x /usr/bin/bazeld`.

# Usage

```sh
bazeld build //...
bazeld --sandbox_debug --verbose_failures test //....
bazeld run //:my_target
```

# Configuration

All configuration is provided via a .bazeldrc file located in the root of the
bazel repo or $HOME directory.

```sh
# Required - The image to run bazel commands in.
IMAGE_NAME=image-name:image-tag
# Optional - Whether to mount GCP credentials and configure the docker container for GCP auth. Defaults to true.
AUTH_GCP=true
# Optional - Whether to configure the docker container for docker-in-docker (running docker commands in a docker container). Defaults to true.
ENABLE_DOCKER_IN_DOCKER=true
# Optional - Whether to run as the current user (self). Defaults to true.
RUN_AS_SELF=true
# Optional - Whether to run as root. Defaults to false.
# If neither this or RUN_AS_SELF is set then we will default to the default user in the docker image.
RUN_AS_ROOT=false
# Optional - Whether to skip mounting the passwd file. Defaults to true.
# If this is false then you will need to make sure that your docker image is configured correctly for docker in docker.
MOUNT_PASSWD=true
```

Example config:
```sh
IMAGE_NAME=gcr.io/bazel-public/bazel:latest
```
