# Understanding "bootc compatible" images

At the current time, it does not work to just do:
```
FROM fedora
RUN dnf -y install kernel
```
or
```
FROM debian
RUN apt install kernel
```

And get an image compatible with bootc.  Supporting this
is an eventual goal, however there are a few reasons why
this doesn't yet work.  The biggest reason is SELinux
labeling support; the underlying ostree stack currently
handles this and requires that the "base image"
have a pre-computed set of labels that can be used
for any derived layers.

# Building bootc compatible base images

As a corollary to this, the build process
for generating base images currently requires running
through ostree tooling to generate an "ostree commit"
which has some special formatting in the base image.

However, the ostree usage is an implementation detail
and the requirement on this will be lifted in the future.

For example, the [rpm-ostree compose image](https://coreos.github.io/rpm-ostree/container/#creating-base-images)
tooling currently streamlines this, operating just
on a declarative input and writing to a registry.

This is how the [Project Sagano](https://gitlab.com/CentOS/cloud/sagano)
base images are built.

# Deriving from existing base images

However, it's important to emphasize that from one
of these specially-formatted base images, every
tool and technique for container building applies!
In other words it will Just Work to do
```
FROM <bootc base image>
RUN dnf -y install foo && dnf clean all 
```

## Using the `ostree container commit` command

As an opt-in optimization today, you can also add `ostree container commit`
as part of your `RUN` invocations.   This will perform early detection
of some incompatibilities.

However, its usage is not and will never be strictly required.


