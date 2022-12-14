# Coding club - Docker for reproducibility

How to make simple docker containers for making reproducible and accessible code to share.

## What is a docker container

A Docker container (or just container) runs a linux kernel, on top of which you can install libraries and software to run. Differently from what is done in a package manager, a Docker container virtualizes the OS, that will remain the same across different computers running the container. A Docker is also lightweight and easily shareable, making it useful to share reproducible courses or analysis with others. Docker containers are shared and stored in container images (or just images), which are composed of layers of operations used to build the image (you will see those later). 

## Is a container a virtual machine?

A Docker virtualizes the OS using a linux kernel. A Virtual machine does the same thing: it virtualize an OS, but additionally this runs on virtualized hardware by means of a so-called hypervisor. In this way, a virtual machine runs on exactly the same hardware independently of which computer is used.

![](https://k21academy.com/wp-content/uploads/2020/05/2020_05_13_12_19_07_PowerPoint_Slide_Show_Azure_AZ104_M01_Compute_ed1_-1024x467.png)

[Read more here](https://k21academy.com/docker-kubernetes/docker-vs-virtual-machine/)

## Making a Docker container

To build a container image, you need first of all to [install Docker desktop](https://www.docker.com/products/docker-desktop/) with the installer for either MacOs or Windows (Linux has a guide to install from the terminal). 

To share your containers, make an account on [Docker Hub](https://hub.docker.com/), where you can upload your files which others can download and use immediately.

Once you install and open Docker desktop (it takes a little time to open), start a terminal. You can do various things we look at down here.

### Pulling and running a container from the hub

You can pull from Docker hub a container image running Rstudio by pulling it [from its repository](https://hub.docker.com/r/rocker/rstudio):

```
$ docker pull rocker/rstudio:4.1.2

4.1.2: Pulling from rocker/rstudio
eaead16dc43b: Pull complete
35eac095fa03: Pull complete
90e64e55332d: Pull complete
090d6e8058f8: Pull complete
5bad21774007: Pull complete
facff364deac: Pull complete
3f87a6dd047a: Pull complete
Digest: sha256:0b261e81e0605b6dfbdb24cc083f73e67a28b7f35b2184ef9a6ef049013edd68
Status: Downloaded newer image for rocker/rstudio:4.1.2
docker.io/rocker/rstudio:4.1.2
```

#### Image and Layers

A container is shared through so-called images. When you run an image, you get a container on which software is executed. An image is composed of many layers (the ones for which the output above says `Pull complete`). Each layer is a set of operations done to build the image (e.g. installations of packages, copy of files).

#### Tags and Base layers

We pulled an image from `rocker/rstudio`. The name `rocker` is just the username which uploaded the image `rstudio` on Docker Hub. This image is actually built on top of another image called `rocker/r-ver`, which contains only `R` but not an installation of Rstudio, and has the base layers that are reused for the `rstudio` image. There is also an image called `rocker/tidyverse`, built on top of `rocker/rstudio` but containing preinstalled `tidyverse` packages.

 `rocker/rstudio` has in our case also the tag `:4.1.2`, meaning we pulled the image with the version `4.1.2` of `R`. If you use no tags, then the default is for the latest version (tag `latest`). Other tags are illustrated in the documentation for a specific image. See the [documentation](https://rocker-project.org/images/versioned/rstudio.html) for the image we downloaded.

#### Running the image

Now you can `run` the container from the `rstudio` image by using the following command. It will give you a password in the command line. Use it together with the username `rstudio` when you login from your browser at the address [localhost:8787](localhost:8787).

```
docker run --rm \
           -it \
           -p 8787:8787 \
           --name rstudio \
           rocker/rstudio:4.1.2
```

The options above mean:

- `--rm` cleanup after you close the docker image
- `-it`  interactive run using command line interface
-  `-p 8787:8787` communication ports for access to Rstudio
- `--name rstudio` name for the container you are running

#### Mounting a folder when running a container

If you want to use the container to work on various files in a folder, you can mount such folder when you `run` the container with the option `-v`. For example

```
docker run --rm \
           -it \
           -p 8787:8787 \
           --name rstudio \
           -v ~/Repositories/Docker_tutorial/Docker_Rstudio/dataRstudio:/home/rstudio/ \
           rocker/rstudio:4.1.2
```
where the folder before `:` is the local folder, and the one after `:` is the folder inside the container (in this case the one used as default in Rstudio). NOte that folders must **always** be written in absolute path!

#### Useful commands

Use `docker stats` to see memory and cpu usage and other informations about your containers. You will also be able to see the memory limit you have available (you can change that into preferences in Docker Desktop).

With `docker images` and `docker ps` to find out how many images and containers you have in your computer. Remove them using either `docker rmi` or `docker rm`. You can do those operations also interactively in Docker Desktop. You can stop a container with `docker stop` and restart it with `docker restart`.

Write `docker --help` to see all commands in Docker.

[Docker cheatsheet 1](https://dockerlabs.collabnix.com/docker/cheatsheet/)
[Docker cheatsheet 2](https://docs.docker.com/get-started/docker_cheatsheet.pdf)

### Build a container from a base layer

We would like to have a container in which we have our own package and folders. Starting from some base layers, like `rocker/Rstudio`, we can create a personalized image with packages and files we need.

Create a folder, and in that a file called `Dockerfile`. You need no more to start, just write inside `Dockerfile`. All accessory files you need for your container We can create some layers to install packages: we need to write commands that we would normally run in the bash command line, all introduced by the Docker instruction `RUN`.

```
FROM rocker/rstudio:4.1.2

USER 0

RUN R -q -e 'install.packages("curl")'  && \
    R -q -e 'install.packages("dplyr")'
```

The command `FROM` sets a base image which layers are included in your own final image. The following Docker commands create other layers in your image.

`USER` sets which user runs the following commands: `0` is the default user (here also called `rstudio`). This is a non-root user. It is good practice to use non-root users running a container, because running as root might give to hackers access to your local computer. Avoid thus the command `USER root`.

`RUN` executes bash commands, here installing two packages in `R`. Note that we have only one `RUN`. Since each `RUN` creates a layer, which can take up quite some memory and resources, we want to reduce the number of layers as much as possible. In general, two layers take more resources to build and store them than a single layer.

You do not need any command to open `Rstudio`: this is already written in the base image. Now set the working directory of your terminal where you have your docker file, and build the container image:

```
docker build -t rstudiotest:1.0 .
```

The option `-t` is the name of your image. Here we put the tag `1.0`  to save a specific tag with version number. If you want your image to be uploaded on Docker Hub, then the name must be in the format `yourusername/rstudiotest:1.0`. Note `.`, which says the docker file is in the current directory. When you are done, run the container:

```
docker run --rm -it -p 8787:8787 --name rstudio rstudiotest:1.0
```

You should be able to load the library `dplyr` we just installed.

#### Copying files into the image

Copying files into your image is pretty easy. Here we want to copy a folder into the container folder `/home/rstudio`:

```
FROM rocker/rstudio:4.1.2

USER 0

RUN R -q -e 'install.packages("curl")'  && \
    R -q -e 'install.packages("dplyr")'

COPY --chown=rstudio:rstudio ./dataRstudio /home/rstudio/

RUN curl https://raw.githubusercontent.com/tidyverse/dplyr/main/vignettes/colwise.Rmd > /home/rstudio/colwise.Rmd

WORKDIR /home/rstudio
```

In this case, we use `COPY` to create a layer in which we move a folder into `/home/rstudio`. The path of the local folder must be starting into the same folder as the `Dockerfile`. There is at the moment no other way of doing that. Also, `--chown=rstudio:rstudio` tells you that the user `rstudio` can access and modify the folder content.

Below, we get some data in the same folder by downloading it with `curl`, thus creating another layer.

Finally, `WORKDIR` sets the current directory.