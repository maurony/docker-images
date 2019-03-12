FROM rocker/rstudio:latest

# forecasting libraries
#RUN Rscript -e "install.packages(c('forecast', 'smooth', 'forecTheta'))"

# data processing libraries
#RUN Rscript -e "install.packages(c('tidyr', 'dplyr', 'tibble', 'lubridate'))"

# parallelisation packages
#RUN Rscript -e "install.packages(c('foreach', 'doSNOW', 'parallel'))"

# visualization
#RUN Rscript -e "install.packages('ggplot2')"

# progress visualization
#RUN Rscript -e "install.packages('progress')"

RUN install2.r --error \
    --deps TRUE \
    RevoScaleR \
    AzureML \
    forecast \
    smooth \
    forecTheta \
    tidyr \
    dplyr \
    tibble \
    lubridate \
    foreach \
    doSNOW \
    parallel \
    doSNOW \
    ggplot2 \
    progress

LABEL org.label-schema.license="RStudio" \
    	org.label-schema.vendor="Rstudio with packages for time series forecasting, Dockerfile provided by Yves Mauron" \
	org.label-schema.name="RStudio" \
	org.label-schema.description="RStudio." \ 
	org.label-schema.vcs-url=$VCS_URL \
	org.label-schema.vcs-ref=$VCS_REF \
	org.label-schema.build-date=$BUILD_DATE \
	org.label-schema.schema-version="rc1" \
	maintainer="Yves Mauron <ypmauron@gmail.com>"
