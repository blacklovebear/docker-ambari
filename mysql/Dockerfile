FROM mysql:5.6.36

COPY ./mysqld.cnf /etc/mysql/mysql.conf.d/mysqld.cnf
COPY ./mysql.cnf /etc/mysql/conf.d/mysql.cnf

ADD schema.sql /docker-entrypoint-initdb.d

EXPOSE 3306