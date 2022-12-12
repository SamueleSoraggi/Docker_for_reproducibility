# Coding club - Docker for reproducibility

How to make simple docker containers for making reproducible and accessible code to share.

## What is a docker container

A Docker container (or just Docker) runs a linux kernel, on top of which you can install libraries and software to run. Differently from what is done in a package manager, a Docker container virtualizes the OS, that will remain the same across different computers running the container. A Docker is also lightweight and easily shareable, making it useful to share reproducible courses or analysis with others.

## Is a container a virtual machine?

A Docker virtualizes the OS using a linux kernel. A Virtual machine does the same thing: it virtualize an OS, but additionally this runs on virtualized hardware by means of a so-called hypervisor. In this way, a virtual machine runs on exactly the same hardware independently of which computer is used.

## Making a Docker container

To build a Docker, you need first of all to [install Docker desktop](https://www.docker.com/products/docker-desktop/) with the installer for either MacOs or Windows (Linux has a guide to install from the terminal). To share your containers, make an account on [Docker Hub](https://hub.docker.com/), where you can upload your final containers which others can download and use immediately.

### How does Docker desktop work

Once you install and open Docker desktop, open a terminal. You can do various things we look at down here.

For example, you can get from jupyterhub a Docker running Rstudio by pulling it from its repository:

```
$ docker pull rocker/rstudio

```

