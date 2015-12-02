
Custom Hive UDF working with SparkSql

Pre-requisites, get custom jar:

    cd /home/zeppelin/dev
    
    wget https://raw.githubusercontent.com/zeltovhorton/sparkworkshop/master/jars/HiveUtils-1.0-SNAPSHOT-jar-with-dependencies.jar
    
    /home/zeppelin/prerequisites/apache-maven-3.3.3/bin/mvn org.apache.maven.plugins:maven-install-plugin:2.5.2:install-file  -Dfile=/home/zeppelin/dev/HiveUtils-1.0-SNAPSHOT-jar-with-dependencies.jar  -DgroupId=com.github.gbraccialli  -DartifactId=HiveUtils  -Dversion=1.0-SNAPSHOT  -Dpackaging=jar


In zeppelin:

    z.load("com.github.gbraccialli:HiveUtils:1.0-SNAPSHOT")

CLI:

    spark-shell --master yarn-client --packages "com.github.gbraccialli:HiveUtils:1.0-SNAPSHOT"
    
    val sqlContext = new org.apache.spark.sql.hive.HiveContext(sc);
    sqlContext.sql("""create temporary function geohash_encode as 'com.github.gbraccialli.hive.udf.UDFGeohashEncode'""");
    sqlContext.sql("""select geohash_encode(1.11,1.11,3) from sample_07 limit 10""").collect().foreach(println);

> Written with [StackEdit](https://stackedit.io/).

