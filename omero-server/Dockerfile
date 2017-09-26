FROM ubuntu:16.04

RUN sed 's/main$/main universe/' -i /etc/apt/sources.list

RUN apt-get update && apt-get upgrade -y && apt-get install -y \
    unzip wget vim supervisor \
    python2.7 python-pil python-matplotlib python-pip \
    python-numpy python-tables python-scipy \
    openjdk-8-jre-headless ice-services  python-zeroc-ice \
    mencoder nano postgresql-client postgresql-client-common

ENV BRANCH 5.3.4
ENV ICE 3.6

# Path variables
ENV OMERO_DIR /omero
ENV OMERO_HOME $OMERO_DIR/OMERO.server

# Add omero user and install omego tools
RUN useradd -m omero
RUN pip install -U pip
RUN pip install -U omego

RUN mkdir $OMERO_DIR
RUN chown omero $OMERO_DIR

# Download Omero with omego
USER omero
WORKDIR $OMERO_DIR

RUN omego download --branch=$BRANCH --ice $ICE --unzipdir . -v server
RUN rm OMERO.server*zip

RUN ln -s OMERO.server-* $OMERO_HOME

# Path variables
ENV OMERO_DATA_DIR /data/omero_data
ENV OMERO_VAR_DIR /data/omero_var
ENV OMERO_SCRIPTS_DIR /data/omero_scripts

# Default password for admin user (root) of OMERO.server
ENV PASSWORD password

COPY start.sh /start.sh
COPY init.sh /init.sh

EXPOSE 4064

CMD ["bash", "/start.sh"]
