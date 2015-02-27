# scalding2spark
<pre>
Migrate from Scalding (aka TypedPipe ) to Spark (aka RDD ), or vice-versa

NOTES: sc = Spark Context, rdd = RDD, pipe = TypedPipe, Tsv = Tab separated File, CC = case class

REPL:
spark/bin/spark-shell --master spark://IP
scalding/scripts/scald.rb --repl --hdfs --host IP

Scalding Imports:
import TDsl._
import com.twitter.scalding._

Spark Imports:
import org.apache.spark.{SparkContext, SparkConf}
import org.apache.spark.SparkContext._
import org.apache.spark.rdd._

API:
1. CONVERT RDD to local lazy list
rdd.toLocalIterator.toStream
pipe.toIterableExecution.waitFor(Config.default, Local(true)).get.toStream

2. SAVE RDD to filesystem as plaintext
rdd.saveAsTextFile("foo")
pipe.write(TypedTsv[String]("foo"))

3. READ RDD from a plaintext file
sc.textFile("foo", 4).cache().map{ x:String => CC(str) }
TypedPipe.from(TextLine("foo")).map{ x: String => CC(x) }

4. MAP, FLATMAP, FILTER ( Identical API )
rdd.map, rdd.flatMap, rdd.filter
pipe.map, pipe.flatMap, pipe.filter

5. SIZE
rdd.count
typedpipe.groupAll.size

6. LIST2RDD ( Convert local Scala list to an RDD )
sc.parallelize( list )
TypedPipe.from(list)

7. INNER JOIN
import org.apache.spark.SparkContext._ ( for implicit conversion of RDD to PairRDDFunctions)
brdd.groupBy{ x=> x.key }.join(ardd.groupBy{ x=> x.key})
pipe1.groupBy( x=> x.key }.join(pipe2.groupBy{ x=> x.key })

8. LEFT JOIN
import org.apache.spark.SparkContext._ ( for implicit conversion of RDD to PairRDDFunctions)
brdd.groupBy{ x=> x.key }.leftOuterjoin(ardd.groupBy{ x=> x.key})
pipe1.groupBy( x=> x.key }.leftJoin(pipe2.groupBy{ x=> x.key })

9. CARTESIAN PRODUCT
ardd.cartesian(brdd)
pipe1.cross(pipe2)

10. CATAMORPHISM
rdd.fold(init){(a,b) => op(a,b) }
pipe.groupAll.foldLeft(init){ (a,b) => op(a,b) }

12. DISTINCT
rdd.distinct
pipe.groupAll.distinct

13. VALUES FROM JOIN
pairedRDD.values
joinedPipe.values

14. TAKE
rdd.take(n)
pipe.limit(n)

15. SAMPLE STATISTICS
On an RDD[Double]
import org.apache.spark.SparkContext._ ( for implicit conversion of RDD[Double] to DoubleRDDFunctions)
rdd.mean, rdd.variance, rdd.stdev, rdd.histogram(buckets)

On a TypedPipe[Double]:
import TDsl._
pipe.toPipe('f).groupAll { _.sizeAveStdev('f -> ('size,'avg,'stdev)) }.toTypedPipe[(Int,Double,Double)]('size,'avg,'stdev)

</pre>

