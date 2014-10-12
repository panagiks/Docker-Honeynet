#Logger
#start with ubuntu
FROM ubuntu:latest

MAINTAINER Spenser Reinhardt
ENV DEBIAN_FRONTEND noninteractive

ADD setup-linux.sh /root/setup-linux.sh

RUN mkdir -p /opt/dionaea/ && \
mkdir -p /opt/kippo/ && \
mkdir -p /opt/myhoneypot/ && \
mkdir -p /opt/conpot && \
mkdir -p /opt/thug/

RUN cd /root/ && \
bash ./setup-linux.sh -s 192.168.99.21 -p 5544 && \
bash ./setup-linux.sh -s 192.168.99.21 -p 5544 -f /opt/dionaea/var/log/dionaea.log -t dionaea && \
bash ./setup-linux.sh -s 192.168.99.21 -p 5544 -f /opt/dionaea/var/log/dionaea-errors.log -t dionaea_error && \
bash ./setup-linux.sh -s 192.168.99.21 -p 5544 -f /opt/kippo/log/kippo.log -t kippo && \
bash ./setup-linux.sh -s 192.168.99.21 -p 5544 -f /opt/myhoneypot/log/glastopf.log -t glastopf && \
bash ./setup-linux.sh -s 192.168.99.21 -p 5544 -f /opt/conpot/var/conpot.log -t conpot && \
bash ./setup-linux.sh -s 192.168.99.21 -p 5544 -f /opt/thug/var/log/thug.log -t thug

#Post-build docker info
WORKDIR /var/log/
ENTRYPOINT ["/usr/sbin/rsyslogd"]
CMD ["-n"]
