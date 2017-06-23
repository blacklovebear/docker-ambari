# FROM hortonworks/ambari-base:7.2-v7
FROM registry.cn-hangzhou.aliyuncs.com/tospur/amb-base
MAINTAINER Hortonworks

# add ambari repo
ADD ambari.repo /etc/yum.repos.d/


RUN yum install -y sshpass mysql-connector-java* ambari-server \
    && yum -y install ntp \
    && yum clean all \
    && curl -o /tmp/ambari-shell.jar \
        https://s3-eu-west-1.amazonaws.com/maven.sequenceiq.com/releases/com/sequenceiq/ambari-shell/0.1.25/ambari-shell-0.1.25.jar

# add ambari shell to the image so new users don't need the 1GB java image
ADD shell/install-cluster.sh /tmp/
ADD shell/wait-for-host-number.sh /tmp/
ADD shell/ambari-shell.sh /tmp/

ENV PLUGIN_PATH /plugins
WORKDIR /tmp

ADD init/init-server.sh /opt/ambari-server/init-server.sh
RUN chmod u+x /opt/ambari-server/init-server.sh

# add mysql and psql connectors to ambari-server so it can be downloaded by services (e.g.: Ranger)
ADD http://dev.mysql.com/get/Downloads/Connector-J/mysql-connector-java-5.1.39.tar.gz /var/lib/ambari-server/resources/mysql-jdbc-driver.jar
ADD https://jdbc.postgresql.org/download/postgresql-9.4.1208.jre7.jar /var/lib/ambari-server/resources/postgres-jdbc-driver.jar

ADD init/ambari-server.service /etc/systemd/system/ambari-server.service
ADD ntp.conf /etc/ntp.conf

RUN systemctl enable ambari-server \
    && systemctl enable ntpd \
    && echo DefaultEnvironment=\"JAVA_HOME=$JAVA_HOME\" >> /etc/systemd/system.conf

VOLUME /var/log

EXPOSE 8080
