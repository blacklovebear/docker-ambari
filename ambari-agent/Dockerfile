# FROM hortonworks/ambari-base:7.2-v7
FROM registry.cn-hangzhou.aliyuncs.com/tospur/amb-base
MAINTAINER Hortonworks

# add ambari repo
ADD ambari.repo /etc/yum.repos.d/

# prepare for trafodion
RUN yum install -y apr apr-util sqlite expect perl-DBD-SQLite* \
    protobuf xerces-c perl-Params-Validate perl-Time-HiRes gzip lzo lzop unzip

RUN yum install -y ambari-agent \
    && yum -y install ntp \
    && yum clean all

#RUN find /etc/rc.d/rc* -name "*ambari-agent" | xargs rm -v

# add a custom folder to the hadoop classpath
RUN mkdir -p /usr/lib/hadoop/lib
ENV HADOOP_CLASSPATH /data/jars/*:/usr/lib/hadoop/lib/*

ADD public-hostname.sh /etc/ambari-agent/conf/public-hostname.sh
ADD internal-hostname.sh /etc/ambari-agent/conf/internal-hostname.sh
RUN sed -i "/\[agent\]/ a public_hostname_script=\/etc\/ambari-agent\/conf\/public-hostname.sh" /etc/ambari-agent/conf/ambari-agent.ini
RUN sed -i "/\[agent\]/ a hostname_script=\/etc\/ambari-agent\/conf\/internal-hostname.sh" /etc/ambari-agent/conf/ambari-agent.ini

# do not use the docker0 interface
RUN sed -i "s/\"ifconfig\"/\"ifconfig eth0\"/" /usr/lib/python2.6/site-packages/ambari_agent/Facter.py

# Add jars from packer image
ADD dash-azure-storage-2.2.0.jar /usr/lib/hadoop/lib/
ADD gcs-connector-latest-hadoop2.jar /usr/lib/hadoop/lib/

ADD init/init-agent.sh /opt/ambari-agent/init-agent.sh
RUN chmod u+x /opt/ambari-agent/init-agent.sh

ADD init/ambari-agent.service /etc/systemd/system/ambari-agent.service
ADD ntp.conf /etc/ntp.conf

RUN systemctl enable ambari-agent \
    && systemctl enable ntpd

RUN echo DefaultEnvironment=\"JAVA_HOME=$JAVA_HOME\" \"HADOOP_CLASSPATH=$HADOOP_CLASSPATH\" >> /etc/systemd/system.conf

VOLUME /var/log
VOLUME /hadoop