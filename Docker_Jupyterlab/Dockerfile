FROM jupyter/minimal-notebook:latest

LABEL software="jupyterlab example" \
      author="Samuele Soraggi" \
      version="v1.0" \
      license="MIT" \
      description="Jupyterlab and python, R packages"


USER 0

COPY ./dataJupyter /usr/dataJupyter

#fix permissions
RUN fix-permissions "${CONDA_DIR}" && \
    fix-permissions "/home/${NB_USER}" && \
    fix-permissions "/usr/dataJupyter"

#create environments
RUN mamba env create -p "${CONDA_DIR}/envs/example_py" -f /usr/dataJupyter/pyenv.yml && \
    mamba env create -p "${CONDA_DIR}/envs/example_r" -f /usr/dataJupyter/renv.yml && \
    mamba clean --all -f -y

#install kernels
RUN "${CONDA_DIR}/envs/example_py/bin/python" -m ipykernel install --user --name="example_python" --display-name "example (python)" && \
    /opt/conda/envs/example_r/bin/R -e "IRkernel::installspec(user=TRUE, name = 'example_R', displayname = 'example (R)')"

RUN ln -s /usr/dataJupyter/*.ipynb

RUN fix-permissions "/home/${NB_USER}"

USER 1000