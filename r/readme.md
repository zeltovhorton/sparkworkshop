Running SparkR

Before you run SparkR, make sure R is installed on all nodes. For information about how to install R on CentOS, see http://www.jason-french.com/blog/2013/03/11/installing-r-in-linux/. Be sure to set JAVA_HOME.

Launch SparkR:

    su spark
    cd /usr/hdp/2.3.2.1-12/spark/bin
    ./sparkR

This will show output similar to the following:

Welcome to

        ____              __ 
       / __/__  ___ _____/ /__ 
      _\ \/ _ \/ _ `/ __/  '_/ 
     /___/ .__/\_,_/_/ /_/\_\   version  1.5.1 
        /_/ 

Spark context is available as sc, SQL context is available as sqlContext
>
At the R prompt, create a DataFrame and list the first few lines:

    sqlContext <- sparkRSQL.init(sc)
    df <- createDataFrame(sqlContext, faithful)
    head(df)

This will show output similar to the following:

...

     eruptions waiting
    1     3.600      79
    2     1.800      54
    3     3.333      74
    4     2.283      62
    5     4.533      85
    6     2.883      55

Read the “people” DataFrame:

    people <- read.df(sqlContext, "people.json", "json")
    head(people)

This will produce output similar to the following:

     age    name
    1  NA Michael
    2  30    Andy
    3  19  Justin

For additional SparkR examples, see 
https://spark.apache.org/docs/latest/sparkr.html.


> Written with [StackEdit](https://stackedit.io/).




