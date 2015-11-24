

##Spark Workshop Philly Crime Dataset

####**Prerequisites**
#####HDP 2.3.2 Sandbox
#####Spark 1.5.1 TP as per http://hortonworks.com/hadoop-tutorial/apache-spark-1-5-1-technical-preview-with-hdp-2-3/

    cd ~
    wget https://s3.amazonaws.com/ardentex-spark/philadelphia-crime-data-2015-ytd.csv
    hadoop fs -put  ~/philadelphia-crime-data-2015-ytd.csv .
    hadoop fs -ls -h philadelphia-crime-data-2015-ytd.csv


####Start Spark Shell
```
 /root/dev/spark-1.5.1-bin-hadoop2.6/bin/spark-shell --packages com.databricks:spark-csv_2.11:1.1.0,com.databricks:spark-avro_2.10:2.0.1,com.github.gbraccialli:HiveUtils:1.0-SNAPSHOT  --master yarn-client --driver-memory 512m --executor-memory 512m
```



## **Load the data**

The first step is to load the data. Run the following cell to create an RDD containing the data.


    val baseRDD = sc.textFile("philadelphia-crime-data-2015-ytd.csv")

 

### Question 1

Does the RDD _actually_ contain the data right now?

    No. RDDs are _lazy_.


## **Explore the data**

Let's take a look at some of the data.



    baseRDD.take(10).foreach(println)


 Okay, there's a header. We'll need to remove that. But we can't just drop the first item. Let's figure out another way to do it.



    val noHeaderRDD = baseRDD.filter { line => ! (line contains "DC_DIST") }



### Metadata

According to the Open Data Philly site, here's what some of those fields actually mean:

Key attribute field names and descriptions

* `DC_DIST` (integer): District number
* `SECTOR` (integer): Sector or PSA Number
* `DISPATCH_DATE` (date string): Date of Incident (modified from original data)
* `DISPATCH_TIME` (time string): Time of Incident (modified from original data)
* `DC_KEY`: (text): Unique ID of each crime
* `UCR_General` (integer): Rounded Crime Code
* `TEXT_GENERAL_CODE` (string): Human-readable Crime Code
* `OBJECTID` (integer): Unique row ID
* `POINT_X` (decimal): Latitude where crime occurred
* `POINT_Y` (decimal): Longitude where crime occurred




### Question 2

Why can't you just do `baseRDD.drop(1)`?

     There _is_ no such methods on RDDs, unlike Scala collections.
     Even if there were, RDDs are _partitioned_, and a `drop()` call would run on every node, dropping the first element of each partition. On one partition, that element would be the header, but not on the other partitions.


    noHeaderRDD.take(10).foreach(println)


 
### Exercise 1

Let's make things a little easier to handle, by converting the `noHeaderRDD` to an RDD containing Scala objects.

**TO DO**

* Split each line into its individual cells.
* Map the RDD into another RDD of appropriate `CrimeData` objects.
* You'll have to decide which fields from the data best map onto the fields of the case class.



TAKE NOTE: We are deliberately only some of the fields we need for
this lab. There's no sense dragging around more data than we need.

    case class CrimeData(dateString: String,
                         timeString: String,
                         offense: String,
                         latitude: String,
                         longitude: String)
    val dataRDD = noHeaderRDD.map { line =>
      val cols = line.split(",")
      CrimeData(dateString = cols(10), //DISPATCH_DATE
                timeString = cols(11), //DISPATCH_TIME
                offense    = cols(6),  //TEXT_GENERAL_CODE
                latitude   = cols(7),  //POINT_X
                longitude  = cols(8))  //POINT_Y
    }
    dataRDD.take(10).foreach(println)



 
### Exercise 2

Next, group the data by type of crime (the `offense` field).



    val groupedByOffenseRDD = dataRDD.groupBy { data => data.offense }

What does this return? You'll need to know for the next step.

    groupedByOffenseRDD.take(10)



But here's a better way. Note the use of Scala's "f" string interpolator,
which is kind of like printf, but with compile-time type safety. For
more information on the "f" interpolator, see
http://docs.scala-lang.org/overviews/core/string-interpolation.html

    val offenseCounts = dataRDD.map(item => (item.offense, 1)).countByKey()
    for ((offense, count) <- offenseCounts) {
      println(f"$offense%30s $count%5d")
    }



## **ETL**
There's some junk in our data. Let's clean it up a bit.



    val BadOffenseRE = """^\d+$""".r
    val cleanedRDD = dataRDD.map { data =>
      data.copy(offense = data.offense.replaceAll("\"", "").trim())
    }.
    filter { data =>
      BadOffenseRE.findFirstIn(data.offense).isEmpty
    }




Next, copy the `groupBy` logic, above, but change it to run against the `cleanedRDD`.



    val groupedByOffenseRDD2 = cleanedRDD.groupBy { data => data.offense }
    
    val offenseCounts2 = cleanedRDD.map(item => (item.offense, 1)).countByKey()
    for ((offense, count) <- offenseCounts2) {
      println(f"$offense%30s $count%5d")
    }


 
### Exercise 3
Next, create an RDD that counts the number of each offense. How many murders were there in 2013? How many car thefts?



But here's a better way. Note the use of Scala's "f" string interpolator,
which is kind of like printf, but with compile-time type safety. For
more information on the "f" interpolator, see
http://docs.scala-lang.org/overviews/core/string-interpolation.html

    val offenseCounts = << FILL THIS IN >>
    for ((offense, count) <- offenseCounts) {
      println(<< FILL THIS IN >>)
    }

Here's one way to do it:

    val offenseCounts = groupedByOffenseRDD2.map { case (offense, items) => (offense, items.size) }.collect()
    for ((offense, count) <- offenseCounts) {
      println(s"$offense: $count")
    }


// But here's a better way. Note the use of Scala's "f" string interpolator,
// which is kind of like printf, but with compile-time type safety. For
// more information on the "f" interpolator, see
// http://docs.scala-lang.org/overviews/core/string-interpolation.html

    val offenseCounts = dataRDD.map(item => (item.offense, item)).countByKey()
    for ((offense, count) <- offenseCounts) {
      println(f"$offense%30s $count%5d")
    }

 ### Question

Run the following cell. Can you explain what happened? Is `collectAsMap()` a _transformation_ or an _action_?



groupedByOffenseRDD2.collectAsMap()



 
### Exercise 4

How many partitions does the base RDD have? What about the `groupedByOffenseRDD2` RDD? How can you find out?

**Hint**: Check the [API documentation](http://spark.apache.org/docs/latest/api/scala/index.html#org.apache.spark.rdd.RDD).



    println(baseRDD.partitions.length)
    println(groupedByOffenseRDD2.partitions.length)



 
### Exercise 5

Since we're continually playing around with this data, we might as well cache it, to speed things up.

**Question**: Which RDD should you cache? 

1. `baseRDD`
2. `noHeaderRDD`
3. `dataRDD`
4. `cleanedRDD`
5. `groupedByOffenseRDD2`
6. None of them, because they're all still too big.
7. It doesn't really matter.



    cleanedRDD.cache()

When you cache nothing will get cached until you run an action , like count()

    cleanedRDD.count()


 ### Exercise 6

Display the number of homicides.

    val resultRDD1 = cleanedRDD.filter { _.offense.toLowerCase contains "homicide" }.map {item => (item.offense, 1) }.reduceByKey(_ + _)
    for ((method, count) <- resultRDD1.collect())
      println(f"$method%10s $count%d")





![Stop Sign](http://i.imgur.com/RdABwEB.png)


We'll resume after we've discussed DataFrames.


## Data Frames - Demonstration

Let's plot murders by month. DataFrames are useful for this one.



 To do this property, we'll need to parse the dates. That will require knowing their format. A quick sampling of the data will help.



cleanedRDD.map(_.dateString).take(30)



 Okay. We can now parse the strings into actual `Date` objects.

**NOTE:** The DataFrame API does _not_ support schemas with `Date` objects in them. We'll need to convert the resulting `Date` to a `java.sql.Timestamp`.



 Let's set up the date/time parser.



import java.text.SimpleDateFormat

val dateFmt = new SimpleDateFormat("yyyy-MM-dd")
val timeFmt = new SimpleDateFormat("HH:mm:ss.S")




 Now, we can create the data frame. We'll start with the `no_header_rdd` and map it slightly differently than we did to create `data_rdd`:



import org.apache.spark.sql.types._
import org.apache.spark.sql._
import sqlContext.implicits._
import java.sql.Timestamp

case class CrimeData2(date:      Option[Timestamp],
                      time:      Option[Timestamp],
                      offense:   String,
                      latitude:  Option[BigDecimal],
                      longitude: Option[BigDecimal])
def parseLatLong(s: String) = {
  try {
    Some(BigDecimal(s))
  }
  catch {
    case _: Exception => None
  }
}

def parseDateTime(fmt: SimpleDateFormat, s: String) = {
  try {
    Some(new Timestamp(fmt.parse(s).getTime))
  }
  catch {
    case _: Exception => None
  }
}

val df = noHeaderRDD.map { line =>
  val cols = line.split(",")
  val timeString = cols(11)
  val dateString = cols(10)
  CrimeData2(parseDateTime(dateFmt, dateString),
             parseDateTime(timeFmt, timeString),
             cols(6), 
             parseLatLong(cols(7)),
             parseLatLong(cols(8)))
}.
map { data =>
  data.copy(offense = data.offense.replaceAll("\"", "").trim())
}.
filter { data =>
  BadOffenseRE.findFirstIn(data.offense).isEmpty
}.
toDF




df.printSchema()



import org.apache.spark.sql.functions._
display( 
  df.filter(lower($"offense") like "%homicide%")
    .select(month($"date").as("month"), $"offense")
    .groupBy($"month").count()
)



 What about all crimes per month?



display( df.select(month($"date").as("month")).groupBy("month").count() )



 We can also plot the frequency of crimes by hour of day.



display(df.select(hour($"time").as("hour"), $"offense").groupBy($"hour").count())




> Written with [StackEdit](https://stackedit.io/).










