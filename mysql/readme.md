running mysql on docker:
https://hub.docker.com/r/ubuntu/mysql

https://github.com/vbabak/docker-mysql-master-slave/blob/master/build.sh



version: '3'
services:
  mysql_master:
    image: mysql:8.0
    env_file:
      - ./master/mysql_master.env
    container_name: "mysql_master"
    restart: "no"
    ports:
      - 4406:3306
    volumes:
      - ./master/conf/mysql.conf.cnf:/etc/mysql/conf.d/mysql.conf.cnf
      - ./master/data:/var/lib/mysql
    networks:
      - overlay

  mysql_slave:
    image: mysql:8.0
    env_file:
      - ./slave/mysql_slave.env
    container_name: "mysql_slave"
    restart: "no"
    ports:
      - 5506:3306
    depends_on:
      - mysql_master
    volumes:
      - ./slave/conf/mysql.conf.cnf:/etc/mysql/conf.d/mysql.conf.cnf
      - ./slave/data:/var/lib/mysql
    networks:
      - overlay

networks:
  overlay:







On Master Node:
CREATE USER "mydb_slave_user"@"%" IDENTIFIED BY "mydb_slave_pwd";
GRANT REPLICATION SLAVE ON *.* TO "mydb_slave_user"@"%";
FLUSH PRIVILEGES;




On Slave Node:
show master status;
stop slave;
CHANGE MASTER TO MASTER_HOST = 'mysql_master' , MASTER_USER = 'mydb_slave_user', MASTER_PASSWORD='mydb_slave_pwd', MASTER_LOG_FILE='1.000001', MASTER_LOG_POS=157 ;
start slave;


