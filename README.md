## Spark  ##

#### SPARK SHELL - SCALA CLI

    /root/dev/spark-1.5.1-bin-hadoop2.6/bin/spark-shell --packages com.databricks:spark-csv_2.11:1.1.0,com.databricks:spark-avro_2.10:2.0.1,com.github.gbraccialli:HiveUtils:1.0-SNAPSHOT  --master yarn-client --driver-memory 512m --executor-memory 512m

#### PYSPARK - PYTHON CLI 

## ZEPPELIN 

#### ZEPPELIN - AMBARI 1.4.1 

#### ZEPPELIN FOR SPARK 1.5.1 TP 

https://zeppelin.incubator.apache.org/docs/install/yarn_install.html
https://github.com/apache/incubator-zeppelin

mvn clean package -Pspark-1.5 -Dhadoop.version=2.6.0 -Phadoop-2.6  -Pyarn -DskipTests


    cd /root/dev/incubator-zeppelin
    ./bin/zeppelin-daemon.sh start

http://sandbox.hortonworks.com:8081/#/

> Written with [StackEdit](https://stackedit.io/).
