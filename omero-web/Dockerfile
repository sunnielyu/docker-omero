FROM ubuntu:16.04

RUN sed 's/main$/main universe/' -i /etc/apt/sources.list

RUN apt-get update && apt-get upgrade -y && apt-get install -y \
    unzip wget vim supervisor \
    python2.7 python-pil python-matplotlib python-pip \
    python-numpy python-tables python-scipy \
    openjdk-8-jre-headless ice-services  python-zeroc-ice \
    mencoder nano postgresql-client postgresql-client-common \
    nginx-full supervisor python-pip openssl

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

ENV OMERO_WEB_CERTS_DIR /data/web_certs
ENV OMERO_WEB_USE_SSL yes
ENV OMERO_WEB_DEVELOPMENT no
ENV OMERO_WEB_DEVELOPMENT_APPS /data/omero_web_apps

RUN pip install -r $OMERO_HOME/share/web/requirements-py27-nginx.txt
RUN pip install django-debug-toolbar
COPY nginx_omero.conf $OMERO_HOME/nginx_omero.conf
COPY nginx_omero_ssl.conf $OMERO_HOME/nginx_omero_ssl.conf

USER root
RUN rm /etc/nginx/sites-enabled/default
RUN mv $OMERO_HOME/nginx_omero.conf /etc/nginx/sites-available/nginx_omero.conf
RUN mv $OMERO_HOME/nginx_omero_ssl.conf /etc/nginx/sites-available/nginx_omero_ssl.conf

RUN echo "daemon off;" >> /etc/nginx/nginx.conf

COPY supervisor.conf /supervisor.conf
COPY start_nginx.sh /start_nginx.sh
COPY start_omero_web.sh /start_omero_web.sh
RUN chown omero /start_omero_web.sh

CMD ["/usr/bin/supervisord", "-c", "/supervisor.conf", "-e", "info"]
