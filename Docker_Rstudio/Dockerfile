FROM rocker/rstudio:4.1.2

USER 0

RUN R -q -e 'install.packages("curl")'  && \
    R -q -e 'install.packages("dplyr")'

COPY --chown=rstudio:rstudio ./dataRstudio /home/rstudio/

RUN wget https://raw.githubusercontent.com/tidyverse/dplyr/main/vignettes/colwise.Rmd -o /home/rstudio/colwise.Rmd

WORKDIR /home/rstudio