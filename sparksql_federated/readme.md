---
title: Spark Sql Federated Query
---
####**Spark SQL comes with a nice feature called: "JDBC to other Databases", but, it practice, it's JDBC federation feature.**

Example below using sandbox 2.3.2 and spark 1.5.1 TP (https://hortonworks.com/hadoop-tutorial/apache-spark-1-5-1-technical-preview-with-hdp-2-3/):

1- Run Spark SQL Thrift Server with mysql jdbc driver:

    [root@sandbox incubator-zeppelin]# /root/dev/spark-1.5.1-bin-hadoop2.6/sbin//start-thriftserver.sh --hiveconf hive.server2.thrift.port=10010 --jars "/usr/share/java/mysql-connector-java.jar"


2- Open beeline and connect to Spark SQL Thrift Server:

    beeline -u "jdbc:hive2://localhost:10010/default" -n admin

3- Create a jdbc federated table pointing to existing mysql database, using beeline:

    CREATE TABLE mysql_federated_sample
    USING org.apache.spark.sql.jdbc
    OPTIONS (
      driver "com.mysql.jdbc.Driver",
      url "jdbc:mysql://localhost/hive?user=hive&password=hive",
      dbtable "TBLS"
    );
    describe mysql_federated_sample;
    select * from mysql_federated_sample;
    select count(1) from mysql_federated_sample;

####**Code below using spark-shell, scala code and data frames.**

1- Open spark-shell with mysql jdbc driver

    /root/dev/spark-1.5.1-bin-hadoop2.6/bin/spark-shell  --jars "/usr/share/java/mysql-connector-java.jar"

2- Create a data frame pointing to mysql table

    val jdbcDF = sqlContext.read.format("jdbc").options( 
      Map(
      "driver" -> "com.mysql.jdbc.Driver",
      "url" -> "jdbc:mysql://localhost/hive?user=hive&password=hive",
      "dbtable" -> "TBLS"
      )
    ).load()
      
    jdbcDF.show

--NO_CROSS_POST

> Written with [StackEdit](https://stackedit.io/).


