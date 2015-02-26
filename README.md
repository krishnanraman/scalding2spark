# scalding2spark
<pre>
Migrate from Scalding (aka TypedPipe ) to Spark (aka RDD ), or vice-versa

NOTES: sc = Spark Context, rdd = RDD, pipe = TypedPipe, Tsv = Tab separated File, CC = case class

IMPORTS:
Scalding:
import TDsl._
import com.twitter.scalding._

Spark:
import org.apache.spark.{SparkContext, SparkConf}
import org.apache.spark.SparkContext._
import org.apache.spark.rdd._

1. CONVERT RDD to local lazy list
rdd.toLocalIterator.toStream
pipe.toIterableExecution.waitFor.toStream

2. SAVE RDD to filesystem as plaintext
rdd.saveAsTextFile("foo")
pipe.write(TypedTsv[String]("foo"))

3. READ RDD from a plaintext file
sc.textFile("foo", 4).cache().map{ x:String => CC(str) }
TextLine("foo").read.map('line -> 'line) { x: String => CC(x) }.toTypedPipe[CC]('line)

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
new PairRDDFunctions(brdd.groupBy{ x=> x.key }).join(ardd.groupBy{ x=> x.key})
pipe1.groupBy( x=> x.key }.join(pipe2.groupBy{ x=> x.key })

8. LEFT JOIN
new PairRDDFunctions(brdd.groupBy{ x=> x.key }).leftOuterjoin(ardd.groupBy{ x=> x.key})
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

</pre>

