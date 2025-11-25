FROM debian:latest

ENV SINKUSER="lfr_sink_user"
ENV SINKPASS="lfr_sink_password"

ENV DEBIAN_FRONTEND=noninteractive
RUN apt -y update; apt -y install default-mysql-server default-mysql-client

RUN sed -i 's/^.*bind-address.*=.*$/bind-address = 0.0.0.0/g' /etc/mysql/mariadb.conf.d/50-server.cnf

RUN mariadbd-safe & \
    until mysqladmin ping >/dev/null 2>&1; do echo "Waiting for MariaDB to start..."; sleep 1; done && \
    mysql -u root -e "CREATE USER '${SINKUSER}'@'%' IDENTIFIED BY '${SINKPASS}'; GRANT ALL PRIVILEGES ON *.* TO '${SINKUSER}'@'%' WITH GRANT OPTION; FLUSH PRIVILEGES;" && \
    mysql -u root -e "CREATE DATABASE IF NOT EXISTS lfr_sink_db; SET GLOBAL local_infile = true;" && \
    mysql -u root -e "USE lfr_sink_db; CREATE TABLE IF NOT EXISTS lfr_sink_table (a varchar(255));"
EXPOSE 3306

CMD ["mysqld", "--user=mysql"]
