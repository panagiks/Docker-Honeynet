#honeybrid
#start with ubuntu
FROM ubuntu:latest

MAINTAINER Spenser Reinhardt
ENV DEBIAN_FRONTEND noninteractive

RUN sed -i '1ideb mirror://mirrors.ubuntu.com/mirrors.txt trusty main restricted universe multiverse'     /etc/apt/sources.list && \
sed -i '1ideb mirror://mirrors.ubuntu.com/mirrors.txt trusty-updates main restricted universe multiverse' /etc/apt/sources.list && \
sed -i '1ideb mirror://mirrors.ubuntu.com/mirrors.txt trusty-backports main restricted universe multiverse' /etc/apt/sources.list && \
sed -i '1ideb mirror://mirrors.ubuntu.com/mirrors.txt trusty-security main restricted universe multiverse' /etc/apt/sources.list

#dependencies
RUN apt-get update -y && \
apt-get install automake autoconf git-core libtool make build-essential binutils gcc flex byacc libnetfilter-conntrack-dev \
libnetfilter-queue-dev libnetfilter-queue1 libnfnetlink-dev libnfnetlink0 pkg-config libc6-dev libglib2.0-0 libglib2.0-dev \
linux-libc-dev libgloox-dev libxml2-dev libxml++ libpcap0.8-dev libpcap0.8 libdumbnet-dev openssl libssl-dev libev-dev libc6 -y

#honeybrid build and install
RUN cd /tmp/ && \
git clone git://git.code.sf.net/p/honeybrid/git honeybrid-git && \
cd honeybrid-git/ && \
autoreconf -vi && \
./configure "$@" && \
make && \
make install && \
mkdir /etc/honeybrid && \
mkdir /var/log/honeybrid && \
cp honeybrid.conf /etc/honeybrid/ && \
cp honeybrid.sh /etc/init.d/

#add config
ADD honeybrid.cfg /etc/honeybrid/honeybrid.cfg

#cleanup
RUN apt-get clean && rm -rf /var/lib/apt/lists/* /tmp/* /var/tmp/* /install.sh

EXPOSE 80 443
WORKDIR /var/log/honeybrid
VOLUME /var/log/honeybrid
CMD ["/etc/init.d/honeybrid.sh"]
