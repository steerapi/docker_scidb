# COMPILE SciDB 15.7
#
# VERSION 1.0
#
#
#
#
#
#PORT MAPPING
#SERVICE DEFAULT MAPPED
#ssh 			22 22
#Postgresql 	5432 5432
#SciDB 			1239 1239


FROM ubuntu:14.04
MAINTAINER Alber Sanchez


# install
RUN apt-get -qq update && apt-get install --fix-missing -y --force-yes \
	openssh-server \
	openssh-client \
	sudo \
	wget \
	gcc \
	libc-dev-bin \
	libc6-dev \
	libgomp1 \
	libssl-dev \
	linux-libc-dev \
	zlib1g-dev \
	nano \
	gedit \
	postgresql-9.3 \
	dialog \
	curl \
	libcurl3-dev \
	sshpass \
	subversion \
	expect \
	git \
	ssh \
	apt-transport-https



# Set environment
RUN locale-gen en_US.UTF-8
ENV LANG en_US.UTF-8
ENV LANGUAGE en_US:en
ENV LC_ALL en_US.UTF-8
RUN env


# Configure users
RUN useradd --home /home/scidb --create-home --uid 1005 --group sudo --shell /bin/bash scidb
RUN usermod -u 1004 -U scidb
RUN groupmod -g 1004 scidb
RUN usermod -a -G sudo scidb
RUN echo 'root:xxxx.xxxx.xxxx' | chpasswd
RUN echo 'postgres:xxxx.xxxx.xxxx' | chpasswd
RUN echo 'scidb:xxxx.xxxx.xxxx' | chpasswd
RUN echo 'xxxx.xxxx.xxxx'  >> /home/scidb/pass.txt
RUN mkdir /home/scidb/data
RUN chown scidb:scidb /home/scidb/data

# Configure SSH
RUN mkdir /var/run/sshd
RUN echo 'StrictHostKeyChecking no' >> /etc/ssh/ssh_config


# Configure Postgres
RUN echo 'host all all 255.255.0.0/16 md5' >> /etc/postgresql/9.3/main/pg_hba.conf


#Add required files
ADD containerSetup.sh 		/root/containerSetup.sh
ADD conf 	                /root/conf
ADD installR.sh 					/root/installR.sh
ADD scidb_docker.ini 			/home/scidb/scidb_docker.ini
ADD scidb-15.7.0.9267.tgz /home/scidb/dev_dir
ADD installPackages.R			/home/scidb/installPackages.R


RUN chown -R root:root /root/*
RUN chown -R scidb:scidb /home/scidb/*
RUN chmod +x /root/*.sh

# Restarting services
RUN stop ssh
RUN start ssh

RUN apt-get install -y gdebi
RUN sed -i 's/PermitRootLogin without-password/PermitRootLogin yes/' /etc/ssh/sshd_config

# SSH login fix. Otherwise user is kicked off after login
RUN sed 's@session\s*required\s*pam_loginuid.so@session optional pam_loginuid.so@g' -i /etc/pam.d/sshd

ENV NOTVISIBLE "in users profile"
RUN echo "export VISIBLE=now" >> /etc/profile

EXPOSE 22
EXPOSE 8080
EXPOSE 1239
EXPOSE 5432

CMD    ["/usr/sbin/sshd", "-D"]
