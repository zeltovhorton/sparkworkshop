
Custom Hive UDF working with SparkSql

Pre-requisites, get custom jar:

    cd /home/zeppelin/dev
    
    wget https://raw.githubusercontent.com/zeltovhorton/sparkworkshop/master/jars/HiveUtils-1.0-SNAPSHOT-jar-with-dependencies.jar
    
    /home/zeppelin/prerequisites/apache-maven-3.3.3/bin/mvn org.apache.maven.plugins:maven-install-plugin:2.5.2:install-file  -Dfile=/home/zeppelin/dev/HiveUtils-1.0-SNAPSHOT-jar-with-dependencies.jar  -DgroupId=com.github.gbraccialli  -DartifactId=HiveUtils  -Dversion=1.0-SNAPSHOT  -Dpackaging=jar

**Zeppelin Notebook:**

http://sandbox.hortonworks.com:8081/#/notebook/2B2P8RE82

In zeppelin:

    z.load("com.github.gbraccialli:HiveUtils:1.0-SNAPSHOT")

CLI:

    spark-shell --master yarn-client --packages "com.github.gbraccialli:HiveUtils:1.0-SNAPSHOT"
    
    val sqlContext = new org.apache.spark.sql.hive.HiveContext(sc);
    sqlContext.sql("""create temporary function geohash_encode as 'com.github.gbraccialli.hive.udf.UDFGeohashEncode'""");
    sqlContext.sql("""select geohash_encode(1.11,1.11,3) from sample_07 limit 10""").collect().foreach(println);



Custom HiveUDF function:

1- Open spark-shell with hive udf jar as parameter:

    spark-shell --jars path-to-your-hive-udf.jar

2- From spark-shell, open declare hive context and create functions

    val sqlContext = new org.apache.spark.sql.hive.HiveContext(sc);
     
    sqlContext.sql("""CREATE TEMPORARY FUNCTION getRegionUS AS 'com.hortonworks.hive.SimpleUDFgetRegionUS'""");
     sqlContext.sql("""SELECT "California" as State, getRegionUS("California") as Region""").collect().foreach(println);


*This will only work if you also register the json serde as custom udf:*

     sqlContext.sql("""SELECT getRegionUS(split(user.userlocation, ",")[0]) FROM TWEETS""").collect().foreach(println);


IN HIVE:

#### HDP 2.3 ONLY, user is a reserved word, must use set command below with 

upload JAR file into /tmp/udfs on hdfs

    SET hive.support.sql11.reserved.keywords=false;
    
    SELECT "California" as State;
    
    CREATE TEMPORARY FUNCTION getRegionUS AS 'com.hortonworks.hive.SimpleUDFgetRegionUS'
    USING JAR 'hdfs:///tmp/udfs/HiveSimpleUdf-1.0-SNAPSHOT.jar';
    
    LIST JARS;
    
    SELECT "California" as State, getRegionUS("California") as Region;
    
    DROP FUNCTION getRegionsUS;
    
    CREATE FUNCTION getRegionUS AS 'com.hortonworks.hive.SimpleUDFgetRegionUS' 
    USING JAR 'hdfs:///tmp/udfs/HiveSimpleUdf-1.0-SNAPSHOT.jar';
    
    LIST JARS;
    
    SELECT DISTINCT getRegionUS(split(user.userlocation, ",")[0]) FROM TWEETS;

> Written with [StackEdit](https://stackedit.io/).



