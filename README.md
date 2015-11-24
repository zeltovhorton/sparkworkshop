## Spark  Tutorial Using HDP Sandbox ##

####**Prerequisites**
#####HDP 2.3.2 Sandbox
#####Spark 1.5.1 TP as per http://hortonworks.com/hadoop-tutorial/apache-spark-1-5-1-technical-preview-with-hdp-2-3/


#### **SPARK SHELL - SCALA CLI**

    /root/dev/spark-1.5.1-bin-hadoop2.6/bin/spark-shell --packages com.databricks:spark-csv_2.11:1.1.0,com.databricks:spark-avro_2.10:2.0.1,com.github.gbraccialli:HiveUtils:1.0-SNAPSHOT  --master yarn-client --driver-memory 512m --executor-memory 512m

#### **PYSPARK - PYTHON CLI** 

    pyspark

http://hortonworks.com/hadoop-tutorial/hands-on-tour-of-apache-spark-in-5-minutes/

#### **SPARKR - CLI** 


## ZEPPELIN 

#### **ZEPPELIN - AMBARI 1.4.1** 

Install by default on HDP 2.3.2 sandbox

http://hortonworks.com/hadoop-tutorial/interacting-with-data-on-hdp-using-scala-and-apache-spark/

#### **ZEPPELIN FOR SPARK 1.5.1 TP** 

https://zeppelin.incubator.apache.org/docs/install/yarn_install.html
https://github.com/apache/incubator-zeppelin

mvn clean package -Pspark-1.5 -Dhadoop.version=2.6.0 -Phadoop-2.6  -Pyarn -DskipTests


    cd /root/dev/incubator-zeppelin
    ./bin/zeppelin-daemon.sh start

http://sandbox.hortonworks.com:8081/#/

> Written with [StackEdit](https://stackedit.io/).
