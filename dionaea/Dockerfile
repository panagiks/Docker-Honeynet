#dionaea
#start with ubuntu
FROM ubuntu:latest

MAINTAINER Spenser Reinhardt
ENV DEBIAN_FRONTEND noninteractive

#install deps
RUN apt-get update && \
apt-get install libudns-dev libglib2.0-dev libssl-dev libcurl4-openssl-dev libreadline-dev libsqlite3-dev python-dev libtool automake autoconf build-essential subversion git-core flex bison pkg-config wget -y

#make build and install dir
RUN mkdir -p /opt/dionaea
WORKDIR /opt/dionaea/

#liblcfg
RUN git clone git://git.carnivore.it/liblcfg.git liblcfg && \
cd liblcfg/code/ && \
autoreconf -vi && \
./configure --prefix=/opt/dionaea --disable-werror && \
make install

#libemu
RUN git clone git://git.carnivore.it/libemu.git && \
cd libemu/ && \
autoreconf -vi && \
./configure --prefix=/opt/dionaea --enable-python-bindings --disable-werror && \
sed -i 's/ -Werror//g' /opt/dionaea/libemu/src/Makefile && \
make install

#libnl - could be installed via apt, but git is prefered
RUN git clone git://git.infradead.org/users/tgr/libnl.git && \
cd libnl/ && \
autoreconf -vi && \
export LDFLAGS=-Wl,-rpath,/opt/dionaea/lib && \
./configure --prefix=/opt/dionaea --disable-werror && \
make && \
make install

#libev
RUN wget http://dist.schmorp.de/libev/Attic/libev-4.04.tar.gz && \
tar xfzv libev-4.04.tar.gz && \
cd libev-4.04/ && \
./configure --prefix=/opt/dionaea --disable-werror && \
make install

#python 3.3
RUN wget http://www.python.org/ftp/python/3.2.2/Python-3.2.2.tgz && \
tar xfzv Python-3.2.2.tgz && \
cd Python-3.2.2/ && \
./configure --enable-shared --prefix=/opt/dionaea --with-computed-gotos --enable-ipv6 LDFLAGS="-Wl,-rpath=/opt/dionaea/lib/ -L/usr/lib/x86_64-linux-gnu/" && \
make && \
make install

#cython
RUN cd Python-3.2.2/ && \
wget http://cython.org/release/Cython-0.15.tar.gz && \
tar xfzv Cython-0.15.tar.gz && \
cd Cython-0.15/ && \
/opt/dionaea/bin/python3 setup.py install

#libpcap
RUN wget http://www.tcpdump.org/release/libpcap-1.1.1.tar.gz && \
tar xfzv libpcap-1.1.1.tar.gz && \
cd libpcap-1.1.1/ && \
./configure --prefix=/opt/dionaea && \
make && \
make install

#dionaea
RUN git clone git://git.carnivore.it/dionaea.git dionaea && \
cd dionaea/ && \
autoreconf -vi && \
./configure --with-lcfg-include=/opt/dionaea/include/ \
--with-lcfg-lib=/opt/dionaea/lib/ \
--with-python=/opt/dionaea/bin/python3.2 \
--with-cython-dir=/opt/dionaea/bin \
--with-udns-include=/opt/dionaea/include/ \
--with-udns-lib=/opt/dionaea/lib/ \
--with-emu-include=/opt/dionaea/include/ \
--with-emu-lib=/opt/dionaea/lib/ \
--with-gc-include=/usr/include/gc \
--with-ev-include=/opt/dionaea/include \
--with-ev-lib=/opt/dionaea/lib \
--with-nl-include=/opt/dionaea/include \
--with-nl-lib=/opt/dionaea/lib/ \
--with-curl-config=/usr/bin/ \
--with-pcap-include=/opt/dionaea/include \
--with-pcap-lib=/opt/dionaea/lib/ \
--disable-werror \
--with-ssl-lib=/usr/lib/x86_64-linux-gnu/

# changes needed thanks to automake
RUN for file in $(find ./ -name Makefile); do \
	sed -i 's/LIB_SSL_LIBS = -lssl/LIB_SSL_LIBS = -lssl -lcrypto/g' ${file}; \
done

#Make dionaea
RUN make && \
make install

#cleanup
RUN apt-get clean && rm -rf /var/lib/apt/lists/* /tmp/* /var/tmp/* && \
rm -f /opt/dionaea/libpcap-1.1.1.tar.gz /opt/dionaea/Python-3.2.2/Cython-0.15.tar.gz /opt/dionaea/Python-3.2.2.tgz /opt/dionaea/libev-4.04.tar.gz 

#post build
EXPOSE 80 443
ENTRYPOINT ["/opt/dionaea/bin/dionaea"]
CMD ["-l", "all", "-L", "'*'", "-u", "nobody", "-g", "nogroup", "-r", "/opt/dionaea/", "-w", "/opt/dionaea/", "-p", "/opt/dionaea/var/dionaea.pid", "-c", "/opt/dionaea/etc/dionaea/dionaea.conf" ]
