---
title: "Building multi-arch Docker images with buildx"
author: ["Hung Pham"]
date: 2021-02-06
toc: "t"
draft: false
---

Recently at Eureka Robotics, we need to build Docker images for
devices running on `arm` architecture. Fortunately, this is possible
using the new build engine `BuildKit` that the folks at Docker has
been developing in the last few years. (I believe [this GitHub issue](https://github.com/moby/moby/issues/34227) is
the starting point of this effort).

While it is possible to work with the `BuildKit` daemon directly, it
is not the most convenient approach. In fact, Docker's own
documentation suggests two main alternatives:

-   the [`buildx`](https://github.com/docker/buildx) CLI plugin;
-   set `DOCKER_BUILDKIT=1` and use `docker build` ([reference](https://docs.docker.com/develop/develop-images/build%5Fenhancements/)).

At the time of this writing, both approaches use `BuildKit`
internally. However, the details are different.

`buildx` creates `buildkit` daemons in docker containers and
communicates with them. `buildx` manages the life-cycle of buildkit
daemons: creating, configuring, starting, stopping and
removal. Because the daemons are created by `buildx` itself, building
mutli-arch images is possible.

`docker build` with `DOCKER_BUILDKIT=1` in contrast uses the
`buildkit` binary shipped with the `docker` binary. The life cycle is
managed by `docker` itself and thus has a more limited level of
customisation. Perharps this is why building mutli-arch images is not
possible with this approach.


## Building images with buildx `test` {#building-images-with-buildx-test}


### Buildx overview {#buildx-overview}

After installation, `buildx` functionality is available via the
`docker buildx` command group. Many common operations are
available. For example, the sub-command `du` prints disk usage by
`buildx`; the sub-command `prune` allows us to prune away unused
build caches.

One of the main difference that `buildx` introduces is the concept
of _builders_. A builder is fundamentally is an isolated build
environment that is responsible for building images.  Sub-commands
`ls`, `create`, `inspect`, `rm`, `stop` and `use` are for managing
builders' life-cycles.

Core functionalities of a builder instance are implemented by its
_driver_. Currently there are only two drivers available: `docker`
and `docker-container`. While both drivers are "docker", they are
different in the way builds are to be performed by builders.

A builder that uses the `docker` driver build images using the "old
build" functionality provided by the `docker` binary. Hence, this
builder shares the same image space as generic `docker` commands
such as `docker ls`.

the default one used by the "traditional" docker
daemon. `docker-container` driver spins a docker container running
a `BuildKit` docker image.

To learn more about what builders are available on your PC, use
`buildx ls`. To inspect a builder, use the `buildx inspect`
command. Below we inspect the default builder which uses the
`docker` driver.

```shell
docker buildx inspect default
# Name:   default
# Driver: docker
#
# Nodes:
# Name:      default
# Endpoint:  default
# Status:    running
# Platforms: linux/amd64, linux/386

```

Notice that the default builder can only work on `linux/amd64` and
`linux/386`. It can't build image for `arm` architecture. Also
notice that the default builder use the docker driver.

This is the inspect result for another builder on my computer which
can build for `arm`.

```shell
docker buildx inspect builder-test
# Name:   builder-test
# Driver: docker-container

# Nodes:
# Name:      builder-test0
# Endpoint:  unix:///var/run/docker.sock
# Status:    running
# Platforms: linux/amd64, linux/arm64, linux/386

```


### Building images with buildx {#building-images-with-buildx}

Using buildx to build image ([main reference](https://www.docker.com/blog/getting-started-with-docker-for-arm-on-linux/)) is slightly different
from the usual `docker build` experience. We need to first create
and use new builder using these commands:

```shell
docker buildx create --name builder-test --config buildkitd.toml
docker buildx use builder-test
docker buildx inspect --bootstrap

# ➜  docker-multiarch docker buildx inspect --bootstrap
# [+] Building 5.1s (1/1) FINISHED
#  => [internal] booting buildkit                                                                     5.1s
#  => => pulling image moby/buildkit:buildx-stable-1                                                  4.1s
#  => => creating container buildx_buildkit_builder-test0                                             0.9s
# Name:   builder-test
# Driver: docker-container
#
# Nodes:
# Name:      builder-test0
# Endpoint:  unix:///var/run/docker.sock
# Status:    running
# Platforms: linux/amd64, linux/arm64, linux/386
```

To start the build, we use:

```shell
docker buildx build a-dir-with-Dockerfile
```


### Create new `builder` using configuration file. {#create-new-builder-using-configuration-file-dot}

`buildx` builders that use the `docker-container` driver create a
docker container running `buildkit` daemon. One can pass a buildkit
configuration file at this stage to configure the buildkit daemon.
Example configurations include cache storage size and policy cache.

To create a new builder based on a configuration file. This was use
for example [here](https://github.com/docker/buildx/issues/136) to configure application.

```sh
docker buildx create --config=buildkitd.toml
```

[This](https://github.com/moby/buildkit/blob/master/docs/buildkitd.toml.md) is the official guide for `buildkitd.toml`. To understand this
configuration file, this [note on architecture](https://github.com/moby/moby/issues/32925) of BuildKit is
useful.

To enable Garbage Collection (GC), ensure that the following is
presented in the buildkit configuration file:

```toml
[worker.oci]
  gc = true
  gckeepstorage = 20000
```

This configuration enables GC and set the keep storage to 20000Mb,
which is around 20Gb. This can also be achieved without an
configuration file by setting the `--buildkitd-flags`:

```sh
docker buildx create --buildkitd-flags '--oci-worker-gc --oci-worker-gc-keepstorage 20000'
```

The defauflt GC policy follows a few pruning steps. Details can be
found [here](https://github.com/docker/cli/pull/2864/files).

Unfortunately, I think the only way to gain a better understanding
of how buildx work is to understand how BuildKit and buildx are
developed. This means to know how to use [the go programming
language]({{< relref "go-programming-language" >}}).


## Build docker images for arm via QEMU emulation {#build-docker-images-for-arm-via-qemu-emulation}

Below are the steps I have taken to build docker images for `arm`
architecture on a `amd` machine.

-   Install docker 19.03 for `buildx` support.
-   Install Docker support for QEMU by running

    ```sh
    docker run --privileged --rm tonistiigi/binfmt --install all
    ```

    as suggested in <https://github.com/docker/buildx/>
-   Install `qemu` using `apt-get`

    ```sh
    sudo apt-get install qemu qemu-user-static
    ```
-   We can then test by running

    ```sh
    docker run --rm -t arm64v8/ubuntu uname -m  # Testing the emulation environment
    ```
-   At this stage technically we are able to build images for `arm`
    based systems.
-   Important: If QEMU simulation stops working run `docker run --rm
        --privileged multiarch/qemu-user-static --reset -p yes`. This
    should re-register different hooks for `QEMU` and `binfmt` and the
    simulation should work again ([reference](https://github.com/multiarch/qemu-user-static)).


## Related {#related}

Defintely related to [Docker based build system at Eureka]({{< relref "20210109005128-a_docker_based_build_system_at_eureka" >}}) and [Docker flatten image to hide information]({{< relref "docker-flatten-image-to-hide-information" >}})
