#dionaea with DionaeaFR
#start with ubuntu
FROM ubuntu:trusty

MAINTAINER Kolokotronis Panagiotis

#Add Host folder with dionaea.conf and DionaeaFR's settings.py
RUN mkdir /opt/shared
ADD ~/Dionaea-Docker /opt/shared

#########################################################################
###                           Dionaea                                 ###
#########################################################################

RUN apt-get -y update && \
    apt-get -y upgrade && \
    apt-get -y dist-upgrade && \
    apt-get -y install software-properties-common

#Add PPA
RUN add-apt-repository ppa:honeynet/nightly && \
    apt-get update

#Install Dionaea
RUN apt-get -y install dionaea

#Install dependencies not installed by apt
RUN apt-get -y install postgresql cython unzip sqlite3 sqlite

#Install and configure p0f fingerprinting library
RUN apt-get -y install p0f && \
    mkdir /noneexistent && \
    chown -R nobody:nogroup /nonexistent && \
    mkdir /var/p0f
#sudo p0f -i NET-IFACE -u nobody -Q /tmp/p0f.sock -q -l -d -o /var/p0f/p0f.log && \
#sudo chown nobody:nogroup /tmp/p0f.sock

#Copy dionaea.conf from share folder
RUN cp /opt/shared/dionaea.conf /etc/dionaea/dionaea.conf

#Start dionaea service
#RUN sudo service dionaea start 

#########################################################################
###                           DionaeaFR                               ###
#########################################################################

#Install pip and python-netaddr
RUN apt-get -y install python-pip python-netaddr

#Install dependencies with pip
RUN pip install Django=1.8.11 && \
    pip install pygeoip && \
    pip install django-pagination && \
    pip install django-tables2 && \
    pip install django-compressor && \
    pip install django-htmlmin

#Install benjiec’s django-tables2-simplefilter (forked for ease)
RUN cd /opt && \
    git clone https://github.com/panagiks/django-tables2-simplefilter.git && \
    cd django-tables2-simplefilter && \
    python setup.py install

#Install bro’s pysubnettree (forked for ease)
RUN cd /opt && \
    git clone https://github.com/panagiks/pysubnettree.git && \
    cd pysubnettree && \
    python setup.py install

#Install nodejs and npm
RUN apt-get -y install nodejs npm

#Symlink nodejs to node
RUN ln -s "$(which nodejs)" /usr/bin/node

#Install dependencies with npm
RUN npm install -g less && \
    npm install -g promise

#Download DionaeaFR (forked for ease and changes)
RUN cd /opt && \
    git clone https://github.com/panagiks/DionaeaFR.git

#Download GeoIP information
RUN wget http://geolite.maxmind.com/download/geoip/database/GeoLiteCity.dat.gz && \
    wget http://geolite.maxmind.com/download/geoip/database/GeoLiteCountry/GeoIP.dat.gz && \
    gunzip GeoLiteCity.dat.gz && \
    gunzip GeoIP.dat.gz && \
    mv GeoIP.dat DionaeaFR/DionaeaFR/static && \
    mv GeoLiteCity.dat DionaeaFR/DionaeaFR/static

#Copy settings.py form shared folder
RUN cp /opt/shared/settings.py /opt/DionaeaFR/DionaeaFR/settings.py

#Make dir and pid file for DionaeaFR
RUN mkdir /var/run/dionaeafr && \
    touch /var/run/dionaeafr/dionaeafr.pid

#Ready & Run DionaeaFR
RUN cd DionaeaFR && \
    echo 'yes' | python manage.py collectstatic && \
    python manage.py migrate &&
#sudo python manage.py runserver 0.0.0.0:9985 

#post build
#Depending on deplayment might want to break the range and exclude a few ports
EXPOSE 1-65535

#Run p0f fingerprinting library, start dionaea service, Make dir and pid file for DionaeaFR and run DionaeaFR
CMD p0f -i NET-IFACE -u nobody -Q /tmp/p0f.sock -q -l -d -o /var/p0f/p0f.log && \
    chown nobody:nogroup /tmp/p0f.sock && \
    service dionaea start && \
    mkdir /var/run/dionaeafr && \
    python2.7 /opt/DionaeaFR/manage.py runserver 0.0.0.0:9985 && \
    

#VOLUME /opt/dionaea/var/
#ENTRYPOINT ["/opt/dionaea/bin/dionaea"]
#CMD ["-l", "all", "-L", "'*'", "-u", "nobody", "-g", "nogroup", "-r", "/opt/dionaea/", "-w", "/opt/dionaea/", "-p", "/opt/dionaea/var/dionaea.pid", "-c", "/opt/dionaea/etc/dionaea/dionaea.conf" ]
