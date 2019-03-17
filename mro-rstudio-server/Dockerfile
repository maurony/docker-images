FROM maurony/mro-base:latest

ENV DEBIAN_FRONTEND noninteractive
ARG S6_VERSION
ENV S6_VERSION=${S6_VERSION:-v1.22.0.0}

RUN apt-get update && apt-get -y install gdebi-core \
	gdebi-core \
	wget \
	git \
	apt-utils \
	libcurl4-gnutls-dev \
	libssl-dev \
	libxml2-dev \
    	python-setuptools \
    	sudo \
	&& wget https://s3.amazonaws.com/rstudio-ide-build/server/bionic/amd64/rstudio-server-1.2.1322-amd64.deb \
  	&& gdebi --non-interactive rstudio-server-1.2.1322-amd64.deb \
  	&& rm rstudio-server-*-amd64.deb \
	## Symlink pandoc & standard pandoc templates for use system-wide
	&& ln -s /usr/lib/rstudio-server/bin/pandoc/pandoc /usr/local/bin \
  	&& ln -s /usr/lib/rstudio-server/bin/pandoc/pandoc-citeproc /usr/local/bin \
  	&& git clone https://github.com/jgm/pandoc-templates \
  	&& mkdir -p /opt/pandoc/templates \
  	&& cp -r pandoc-templates*/* /opt/pandoc/templates && rm -rf pandoc-templates* \
  	&& mkdir /root/.pandoc && ln -s /opt/pandoc/templates /root/.pandoc/templates \
  	&& apt-get clean \
  	&& rm -rf /var/lib/apt/lists/ \
  	## RStudio wants an /etc/R, will populate from $R_HOME/etc
  	&& mkdir -p /etc/R \
  	## Write config files in $R_HOME/etc
  	&& echo '\n\
    		\n# Configure httr to perform out-of-band authentication if HTTR_LOCALHOST \
    		\n# is not set since a redirect to localhost may not work depending upon \
    		\n# where this Docker container is running. \
    		\nif(is.na(Sys.getenv("HTTR_LOCALHOST", unset=NA))) { \
    		\n  options(httr_oob_default = TRUE) \
    		\n}' >> /opt/microsoft/ropen/3.5.1/lib64/R/etc/Rprofile.site \
  	&& echo "PATH=${PATH}" >> /opt/microsoft/ropen/3.5.1/lib64/R/etc/Renviron \
  	## Need to configure non-root user for RStudio
  	&& useradd rstudio \
  	&& echo "rstudio:rstudio" | chpasswd \
		&& mkdir /home/rstudio \
		&& chown rstudio:rstudio /home/rstudio \
		&& addgroup rstudio staff \
  	## Prevent rstudio from deciding to use /usr/bin/R if a user apt-get installs a package ??
	&& mkdir /usr/local/lib/R && ln -s /opt/microsoft/ropen/3.5.1/lib64/R/bin/R /usr/local/lib/R \
  	&&  echo 'rsession-which-r=/opt/microsoft/ropen/3.5.1/lib64/R/bin/R' >> /etc/rstudio/rserver.conf \
  	## use more robust file locking to avoid errors when using shared volumes:
  	&& echo 'lock-type=advisory' >> /etc/rstudio/file-locks \
  	## configure git not to request password each time
  	&& git config --system credential.helper 'cache --timeout=3600' \
  	&& git config --system push.default simple \
  	## Set up S6 init system
  	##&& wget -P /tmp/ https://github.com/just-containers/s6-overlay/releases/download/${S6_VERSION}/s6-overlay-amd64.tar.gz \
  	##&& tar xzf /tmp/s6-overlay-amd64.tar.gz -C / \
  	&& mkdir -p /etc/services.d/rstudio \
  	&& echo '#!/usr/bin/with-contenv bash \
          	\n## load /etc/environment vars first: \
  		\n for line in $( cat /etc/environment ) ; do export $line ; done \
          	\n exec /usr/lib/rstudio-server/bin/rserver --server-daemonize 0' \
          	> /etc/services.d/rstudio/run \
  	&& echo '#!/bin/bash \
        	\n rstudio-server stop' \
          	> /etc/services.d/rstudio/finish \
  	&& mkdir -p /home/rstudio/.rstudio/monitored/user-settings \
  	&& echo 'alwaysSaveHistory="0" \
          	\nloadRData="0" \
          	\nsaveAction="0"' \
          	> /home/rstudio/.rstudio/monitored/user-settings/user-settings \
	&& chown -R rstudio:rstudio /home/rstudio/.rstudio

#RUN apt-get -y install libcurl4-gnutls-dev \
#	libssl-dev \
#	libxml2-dev 
 
#RUN adduser rstudio

COPY userconf.sh /etc/cont-init.d/userconf
#COPY disable_auth_rserver.conf /etc/rstudio/disable_auth_rserver.conf
#COPY pam-helper.sh /usr/lib/rstudio-server/bin/pam-helper

EXPOSE 8787
