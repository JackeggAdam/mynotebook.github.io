---
layout: post_markdown
title: Notes on Alpine Linux
description: TODO
tags:
- Alpine Linux
- Docker
- Unix
---

[Alpine Linux](https://alpinelinux.org/) is a popular distribution for creating minimal container images. It describes itself as:

> Alpine Linux is a security-oriented, lightweight Linux distribution based on musl libc and busybox.


The creators of Alpine made a couple of design choices that deviate from a typical Debian-based distro. This post is a summary of what makes Alpine different.


## Alpine building blocks

### musl libc

Probably the main design choice in Alpine is to use [musl libc](https://musl.libc.org/), which is an implementation of the [standard C library (libc)](https://en.wikipedia.org/wiki/C_standard_library) for the Linux kernel. Libc is a wrapper around the [system calls](https://en.wikipedia.org/wiki/System_call) of the operating system kernel, and provides many different operating system services. Libc is a dependency of almost all Linux applications, including Python.

The design choice to use musl differs from distributions like Ubuntu, which use [glibc](https://en.wikipedia.org/wiki/GNU_C_Library), a more popular implementation of libc. One reason why the Alpine creators chose musl is because it provides a much smaller library compared to glibc.

While both musl and glibc implement the functionality described in the C standard library and POSIX standards, musl provides a smaller library compared to glibc. The different implementation also results in different performance behaviour [^libc-impl-comp]. There are also other [functional differences](https://wiki.musl-libc.org/functional-differences-from-glibc.html) between the two outside of the C and POSIX standards.
These differences can lead to incompatibilities between glibc and musl. Binaries need to be compiled against musl in order to work on musl. This also the reason why installing Python PyPI packages is much slower on Alpine[^python-alpine-slow], there are typically no binaries for musl provided so they all need to be rebuild.


### BusyBox

[BusyBox](https://www.busybox.net/) is a package that combines many common Unix utilities into a single small executable. It is optimized with size in mind and is able to provide the most common commands as a small single binary. The BusyBox executable provides commands for Unix applications like `ls`, `grep`, `echo`, `mkdir`, etc. By using BusyBox, Alpine can minimize it size by avoiding to add all the individual implementations of these commands.

BusyBox also provides Alpine's default shell `ash` (or [Almquist shell](https://en.wikipedia.org/wiki/Almquist_shell)). Unlike lots of other distros, Alpine does not come with [Bash](https://en.wikipedia.org/wiki/Bash_(Unix_shell)) pre-installed.


### OpenRC init
- https://news.ycombinator.com/item?id=19375234
- https://www.youtube.com/watch?v=YQtKAk01c0U
- https://en.wikipedia.org/wiki/Init
- https://docs.alpinelinux.org/user-handbook/0.1a/Working/openrc.html
- https://www.reddit.com/r/docker/comments/4ya371/best_way_to_run_multiple_processes_best_init/
- https://wiki.alpinelinux.org/wiki/Writing_Init_Scripts
- https://github.com/OpenRC/openrc
- https://en.wikipedia.org/wiki/OpenRC
- https://wiki.gentoo.org/wiki/OpenRC (look for busybox integration)


### APK
- https://wiki.alpinelinux.org/wiki/Alpine_Linux_package_management
- https://pkgs.alpinelinux.org/package/v3.4/main/x86/apk-tools
- https://git.alpinelinux.org/apk-tools/
- Not all apt packages have apk equivalent
```
apk --no-cache
```

A





## References

* footnotes will be placed here. This line is necessary
{:footnotes}
- [^libc-impl-comp]: [Comparison of C/POSIX standard library implementations for Linux](https://www.etalabs.net/compare_libcs.html)
- [^python-alpine-slow][Using Alpine can make Python Docker builds 50× slower](https://pythonspeed.com/articles/alpine-docker-python/)

## TODO:
- https://alpinelinux.org/
- https://hub.docker.com/_/alpine
- https://hackernoon.com/you-should-use-alpine-linux-instead-of-ubuntu-yb193ujt
- https://www.reddit.com/r/linux/comments/3mqqtx/alpine_linux_why_no_one_is_using_it/
- https://thenewstack.io/alpine-linux-heart-docker/
- Security?
    - For vs against
        - https://www.reddit.com/r/docker/comments/77zork/alpine_vs_debianubuntu_securitywise/
        - https://www.reddit.com/r/docker/comments/9nvs7p/minimal_base_docker_images_compared/
- Python Slower
    - https://pythonspeed.com/articles/alpine-docker-python/
    - https://superuser.com/questions/1219609/why-is-the-alpine-docker-image-over-50-slower-than-the-ubuntu-image
    - https://pythonspeed.com/articles/base-image-python-docker-images/
    - https://lih-verma.medium.com/alpine-makes-python-docker-builds-way-too-50-slower-and-images-double-2-larger-61d1d43cbc79
- https://www.overops.com/blog/my-alpine-desktop-setting-up-a-software-development-environment-on-alpine-linux/


- Minimal container:
  - https://blog.realkinetic.com/building-minimal-docker-containers-for-python-applications-37d0272c52f3


- videos
    - https://www.youtube.com/watch?v=Cc1rBayMnVI
    - https://www.youtube.com/watch?v=sIG2P9k6EjA



- Minimal docker
    - https://phusion.github.io/baseimage-docker/
    - https://github.com/phusion/baseimage-docker
    - Maybe write post about Docker, ps 1, tini, gosu, exec, ...



- Fast API:
    - https://gist.github.com/peterroelants/e344ac416948296f7fcdc84a20ce6eb5#file-python-alpine-dockerfile


- Avoid libc:
    - https://www.jmoisio.eu/en/blog/2020/01/20/programming-without-the-standard-library/
    - https://blogs.oracle.com/linux/hello-from-a-libc-free-world-part-1-v2
    - https://hero.handmade.network/forums/code-discussion/t/861-compiling_without_libc_on_linux
    - https://the-linux-channel.the-toffee-project.org/index.php?page=5-tutorials-a-linux-system-call-in-c-without-a-standard-library
    - https://cloudibee.com/writing-c-software-without-stdlib/