# Creates an Ambari Server base on vanilla centos
#
# docker build -t seq/ambari ambari-server

FROM tianon/centos
MAINTAINER SequenceIQ

ADD hdp.repo /etc/yum.repos.d/
ADD ambari.repo /etc/yum.repos.d/
RUN yum install -y ambari-server ambari-agent
RUN yum install -y tar git curl
RUN mkdir -p /usr/jdk64/ && curl http://public-repo-1.hortonworks.com/ARTIFACTS/jdk-7u45-linux-x64.tar.gz | tar -xz -C /usr/jdk64/
RUN ambari-server setup --silent -j /usr/jdk64/jdk1.7.0_45
#ambari-server setup --silent -j /usr/jdk64/jdk1.7.0_45 -r /var/lib/ambari-server/resources/Ambari-DDL-Postgres-DROP.sql -v

ADD system-auth /etc/pam.d/
RUN sed -i.bak "/^hostname/ s/.*/hostname=server.ambari.com/" /etc/ambari-agent/conf/ambari-agent.ini

#RUN su hdfs hadoop namenode -format
EXPOSE 8080