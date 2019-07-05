# MysqlReplica
Mysql Documentation


DigitalOcean Secure phpMyAdmin on Ubuntu 18.04

MySQL Replicate Data In Real-Time | Access Data Wherever, Whenever‎

1. Server XXX.XXX.XXX.XXX
2. Server YYY.YYY.YYY.YYY


1. MysqlUserName = admin    Table (Master) = WP
2. MysqlUserName = admin    Table (Slave)  = WP

(MysqlUserName it does not matter)




1. Server Configration

  STEP 1

    nano /etc/mysql/mysql.conf.d/mysqld.cnf

    server-id         = 1
    binlog-do-db      = WP
    log_bin           = /var/log/mysql/mysql-bin.log
    expire_logs_days  = 2
    bind-address      = 0.0.0.0

    sudo service mysql restart




  STEP 2

    mysql -u root -p
    CREATE USER 'ReplicaUserName'@'YYY.YYY.YYY.YYY' IDENTIFIED BY 'ReplicaUserPassword';
    GRANT ALL PRIVILEGES ON *.* TO 'ReplicaUserName'@'YYY.YYY.YYY.YYY' IDENTIFIED BY 'ReplicaUserPassword';
    SHOW MASTER STATUS;


      mysql> SHOW MASTER STATUS;
      +------------------+----------+--------------+------------------+-------------------+
      | File             | Position | Binlog_Do_DB | Binlog_Ignore_DB | Executed_Gtid_Set |
      +------------------+----------+--------------+------------------+-------------------+
      | mysql-bin.000001 |     1156 | WP           |                  |                   |
      +------------------+----------+--------------+------------------+-------------------+
      1 row in set (0.00 sec)




2. Server Configration

  STEP 1

    nano /etc/mysql/mysql.conf.d/mysqld.cnf

    server-id       = 2
    replicate-do-db = WP
    binlog-do-db    = WP
    log_bin         = /var/log/mysql/mysql-bin.log
    expire_logs_days= 2
    bind-address    = 0.0.0.0

    sudo service mysql restart



  STEP 2

    mysql -u root -p

    CHANGE MASTER TO MASTER_HOST='XXX.XXX.XXX.XXX',
    MASTER_PORT=3306,
    MASTER_USER='ReplicaUserName',
    MASTER_PASSWORD='ReplicaUserPassword',
    MASTER_LOG_FILE='mysql-bin.000001',
    MASTER_LOG_POS=1156;

    SET GLOBAL SQL_SLAVE_SKIP_COUNTER=1;

    START SLAVE;

    SHOW SLAVE STATUS\G


      Slave_IO_State: Waiting for master to send event
                        Master_Host: XXX.XXX.XXX.XXX
                        Master_User: WP
                        Master_Port: 3306
                      Connect_Retry: 60
                    Master_Log_File: mysql-bin.000001
                Read_Master_Log_Pos: 1156
                     Relay_Log_File: service-MySQL-Slave-relay-bin.000005
                      Relay_Log_Pos: 759
              Relay_Master_Log_File: mysql-bin.000001
                   Slave_IO_Running: Yes
                  Slave_SQL_Running: Yes
                    Replicate_Do_DB: WP
                Replicate_Ignore_DB:
                 Replicate_Do_Table:
             Replicate_Ignore_Table:
            Replicate_Wild_Do_Table:
        Replicate_Wild_Ignore_Table:
                         Last_Errno: 0
                         Last_Error:
                       Skip_Counter: 0
                Exec_Master_Log_Pos: 1156
                    Relay_Log_Space: 980
                    Until_Condition: None
                     Until_Log_File:
                      Until_Log_Pos: 0
                 Master_SSL_Allowed: No
                 Master_SSL_CA_File:
                 Master_SSL_CA_Path:
                    Master_SSL_Cert:
                  Master_SSL_Cipher:
                     Master_SSL_Key:
              Seconds_Behind_Master: 0
      Master_SSL_Verify_Server_Cert: No
                      Last_IO_Errno: 0
                      Last_IO_Error:
                     Last_SQL_Errno: 0
                     Last_SQL_Error:
        Replicate_Ignore_Server_Ids:
                   Master_Server_Id: 1
                        Master_UUID: 1e34598d-d87a-11e8-8f96-724f27e47f41
                   Master_Info_File: /var/lib/mysql/master.info
                          SQL_Delay: 0
                SQL_Remaining_Delay: NULL
            Slave_SQL_Running_State: Slave has read all relay log; waiting for more updates
                 Master_Retry_Count: 86400
                        Master_Bind:
            Last_IO_Error_Timestamp:
           Last_SQL_Error_Timestamp:
                     Master_SSL_Crl:
                 Master_SSL_Crlpath:
                 Retrieved_Gtid_Set:
                  Executed_Gtid_Set:
                      Auto_Position: 0
               Replicate_Rewrite_DB:
                       Channel_Name:
                 Master_TLS_Version:
    exit

    if Last_IO_Error = TRUE (error connecting to master bla bla bla) GO STEP 4


  STEP 3 (if needed)

    1. Server Configration

    sudo ufw status verbose
    sudo ufw allow mysql
    service ufw restart



  STEP 4 (if needed)

    rm -rf /var/lib/mysql/auto.cnf
    service mysql restart


the two data should be the same
