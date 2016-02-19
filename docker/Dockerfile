
FROM debian:latest
MAINTAINER Nils Schmidt <schmidt89@informatik.uni-marburg.de>

ENV WORK_DIR /home/meshadmin
ENV HOME $WORK_DIR
ENV SERVAL_REPO https://github.com/umr-studenten/serval-dna.git
ENV SERVAL_TESTS https://github.com/gh0st42/serval-tests.git
ENV CORE_SERVAL https://github.com/gh0st42/core-serval.git

#####################################################
### Installation
#####################################################

# install dependencies
RUN apt-get update \
	&& apt-get install -y --no-install-recommends bash bridge-utils ebtables iproute libev-dev python tcl8.5 tk8.5 libtk-img autoconf automake gcc libev-dev make python-dev libreadline-dev pkg-config imagemagick help2man\
	&& apt-get install -y git make\
	&& apt-get clean
		
RUN mkdir -p $WORK_DIR
WORKDIR $WORK_DIR

# install core
RUN  git clone https://github.com/coreemu/core.git\
    && cd core\
	&& ./bootstrap.sh\
	&& ./configure\
	&& make\
	&& make install\
	&& make clean

# install serval
RUN mkdir -p $WORK_DIR/.core\
	&& cd $WORK_DIR\
	&& git clone $CORE_SERVAL\
	&& git clone $SERVAL_TESTS\
	&& mv core-serval/dotcore/* $WORK_DIR/.core/\
	&& mkdir serval-conf\
	&& git clone $SERVAL_REPO\
	&& cd serval-dna\
	&& autoreconf -i\
	&& ./configure --prefix=/home/meshadmin/serval-conf\
	&& make -j 2\
	&& make install\
	&& cd ..\
	&& ln -s $WORK_DIR/serval-dna/servald /usr/bin/servald\
	&& echo "custom_services_dir =$WORK_DIR/.core/myservices" >> /etc/core/core.conf\
	&& service core-daemon restart

ADD . $WORK_DIR

ENV PATH $PATH:$WORK_DIR/serval-conf/sbin/
CMD ./docker_start.sh

VOLUME /data

