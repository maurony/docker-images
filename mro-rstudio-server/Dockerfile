FROM maurony/mro-base

RUN apt-get -y install gdebi-core \
	wget \
	apt-utils

RUN wget https://s3.amazonaws.com/rstudio-ide-build/server/bionic/amd64/rstudio-server-1.2.1322-amd64.deb \
  && gdebi --non-interactive rstudio-server-1.2.1322-amd64.deb \
  && rm rstudio-server-*-amd64.deb 

RUN apt-get -y install libcurl4-gnutls-dev \
	libssl-dev \
	libxml2-dev 
 
RUN adduser rstudio

COPY userconf.sh /etc/cont-init.d/userconf

EXPOSE 8787
