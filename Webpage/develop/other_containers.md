# A container with jupyterlab and both R and Python

There are other containers, such as one with `jupyterlab`. This includes `conda` package manager, and other versions of the container images include known packages. [See here the documentation](https://jupyter-docker-stacks.readthedocs.io/en/latest/using/selecting.html).

We try to build an image that installs two conda environments and creates python and R kernels inside jupyterlab. 

```
FROM jupyter/minimal-notebook:latest

LABEL software="jupyterlab example" \
      author="Samuele Soraggi" \
      version="v1.0" \
      license="MIT" \
      description="Jupyterlab and python, R packages"


USER 0

COPY ./dataJupyter /usr/dataJupyter

#fix permissions with a script included in the jupyterlab image
RUN fix-permissions "${CONDA_DIR}" && \
    fix-permissions "/home/${NB_USER}" && \
    fix-permissions "/usr/dataJupyter"

#create environments from yml files and then clean
RUN mamba env create -p "${CONDA_DIR}/envs/example_py" -f /usr/dataJupyter/pyenv.yml && \
    mamba env create -p "${CONDA_DIR}/envs/example_r" -f /usr/dataJupyter/renv.yml && \
    mamba clean --all -f -y

#install kernels from the conda environments
RUN "${CONDA_DIR}/envs/example_py/bin/python" -m ipykernel install --user --name="example_python" --display-name "example (python)" && \
    /opt/conda/envs/example_r/bin/R -e "IRkernel::installspec(user=TRUE, name = 'example_R', displayname = 'example (R)')"

RUN ln -s /usr/dataJupyter/*.ipynb

RUN fix-permissions "/home/${NB_USER}"

#nonroot user
USER 1000
```