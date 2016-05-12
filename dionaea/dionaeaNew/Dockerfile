#dionaea with DionaeaFR
#start with ubuntu
FROM ubuntu:trusty

MAINTAINER Kolokotronis Panagiotis
#ENV DEBIAN_FRONTEND noninteractive

#########################################################################
###                           Dionaea                                 ###
#########################################################################

RUN sudo apt-get -y update && \
sudo apt-get -y upgrade && \
sudo apt-get -y dist-upgrade && \
sudo apt-get install software-properties-common

#Add PPA
RUN sudo add-apt-repository ppa:honeynet/nightly && \
sudo apt-get update

#Install Dionaea
RUN sudo apt-get install dionaea

#Install dependencies not installed by apt
RUN sudo apt-get install postgresql cython unzip sqlite3 sqlite

#Install, configure and start p0f fingerprinting library
RUN sudo apt-get install p0f && \
sudo mkdir /noneexistent && \
sudo chown -R nobody:nogroup /nonexistent && \
sudo mkdir /var/p0f && \
sudo p0f -i NET-IFACE -u nobody -Q /tmp/p0f.sock -q -l -d -o /var/p0f/p0f.log && \
sudo chown nobody:nogroup /tmp/p0f.sock

#Get dionaea.conf from a server REPLACE THE PLACE-HOLDER UDL
RUN sudo wget -O /etc/dionaea/dionaea.conf http://SERVER_URL/root/to/dionaea.conf

#Start dionaea service
RUN sudo service dionaea start 

#########################################################################
###                           DionaeaFR                               ###
#########################################################################

#Install pip and python-netaddr
RUN sudo apt-get install python-pip python-netaddr

#Install dependencies with pip
RUN pip install Django=1.8.11 && \
pip install pygeoip && \
pip install django-pagination && \
pip install django-tables2 && \
pip install django-compressor && \
pip install django-htmlmin

#Install benjiec’s django-tables2-simplefilter (forked for ease)
RUN cd /opt && \
sudo git clone https://github.com/panagiks/django-tables2-simplefilter.git && \
cd django-tables2-simplefilter && \
sudo python setup.py install

#Install bro’s pysubnettree (forked for ease)
RUN cd /opt && \
sudo git clone https://github.com/panagiks/pysubnettree.git && \
cd pysubnettree && \
sudo python setup.py install

#Install nodejs and npm
RUN sudo apt-get install nodejs npm

#Symlink nodejs to node
RUN sudo ln -s "$(which nodejs)" /usr/bin/node

#Install dependencies with npm
RUN npm install -g less && \
npm install -g promise

#Download DionaeaFR (forked for ease and changes)
RUN cd /opt && \
sudo git clone https://github.com/panagiks/DionaeaFR.git

#Download GeoIP information
RUN sudo wget http://geolite.maxmind.com/download/geoip/database/GeoLiteCity.dat.gz && \
sudo wget http://geolite.maxmind.com/download/geoip/database/GeoLiteCountry/GeoIP.dat.gz && \
sudo gunzip GeoLiteCity.dat.gz && \
sudo gunzip GeoIP.dat.gz && \
sudo mv GeoIP.dat DionaeaFR/DionaeaFR/static && \
sudo mv GeoLiteCity.dat DionaeaFR/DionaeaFR/static

#Copy settings.py form template (optionally make changes)
RUN sudo cp DionaeaFR/DionaeaFR/settings.py.dist DionaeaFR/DionaeaFR/settings.py

#Make dir and pid file for DionaeaFR
RUN sudo mkdir /var/run/dionaeafr && \
sudo touch /var/run/dionaeafr/dionaeafr.pid

#Ready & Run DionaeaFR
RUN cd DionaeaFR && \
echo 'yes' | sudo python manage.py collectstatic && \
sudo python manage.py migrate && \
sudo python manage.py runserver 0.0.0.0:9985 

#post build
EXPOSE 1-65535
#VOLUME /opt/dionaea/var/
#ENTRYPOINT ["/opt/dionaea/bin/dionaea"]
#CMD ["-l", "all", "-L", "'*'", "-u", "nobody", "-g", "nogroup", "-r", "/opt/dionaea/", "-w", "/opt/dionaea/", "-p", "/opt/dionaea/var/dionaea.pid", "-c", "/opt/dionaea/etc/dionaea/dionaea.conf" ]
