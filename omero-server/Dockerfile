FROM ubuntu:16.04

RUN sed 's/main$/main universe/' -i /etc/apt/sources.list

RUN apt-get update && apt-get upgrade -y && apt-get install -y \
    software-properties-common python-software-properties \
    db5.3-util libssl-dev libbz2-dev libmcpp-dev libdb++-dev libdb-dev \
    apt-transport-https wget

RUN apt-key adv --keyserver keyserver.ubuntu.com --recv 5E6DA83306132997
RUN apt-add-repository -y "deb http://zeroc.com/download/Ice/3.6/ubuntu16.04 stable main"

RUN apt-get update && apt-get install -y \
    unzip vim supervisor \
    python2.7 python-pil python-matplotlib python-pip python-numpy python-tables python-scipy \
    openjdk-8-jre-headless mencoder postgresql-client postgresql-client-common \
    zeroc-ice-all-runtime zeroc-ice-all-dev

RUN pip install -U pip
RUN pip install "zeroc-ice>3.5,<3.7"

# Path variables
ENV OMERO_DIR /omero
ENV OMERO_HOME $OMERO_DIR/OMERO.server

# Add omero user
RUN useradd -m omero

RUN mkdir $OMERO_DIR
RUN chown omero $OMERO_DIR

USER omero
WORKDIR $OMERO_DIR

RUN wget http://downloads.openmicroscopy.org/latest/omero5.3/server-ice36.zip -O OMERO.server-ice36.zip
RUN unzip -q OMERO.server*
RUN rm OMERO.server*zip

RUN ln -s OMERO.server-* $OMERO_HOME

ENV OMERO_DATA_DIR /data/omero_data
ENV OMERO_VAR_DIR /data/omero_var
ENV OMERO_SCRIPTS_DIR /data/omero_scripts

# Default password for admin user (root) of OMERO.server
ENV PASSWORD password

COPY start.sh /start.sh
COPY init.sh /init.sh

EXPOSE 4064

CMD ["bash", "/start.sh"]
