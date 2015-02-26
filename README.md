# scalding2spark
<pre>
Migrate from Scalding (aka TypedPipe ) to Spark (aka RDD ), or vice-versa

NOTES: sc = Spark Context, rdd = RDD, typedpipe = TypedPipe, Tsv = Tab separated File, CC = case class

IMPORTS:
Scalding:
import TDsl._
import com.twitter.scalding._

Spark:
import org.apache.spark.{SparkContext, SparkConf}
import org.apache.spark.SparkContext._
import org.apache.spark.rdd._

CONVERT RDD to local lazy list
1. rdd.toLocalIterator.toStream <-> typedpipe.toIterableExecution.waitFor.toStream

SAVE RDD to filesystem as plaintext
2. rdd.saveAsTextFile("foo") <-> typedpipe.write(TypedTsv[String]("foo"))

READ RDD from a plaintext file
3. sc.textFile("foo", 4).cache().map{ x:String => CC(str) } <-> TextLine("foo").read.map('line -> 'line) { x: String => CC(x) }.toTypedPipe[CC]('line)

MAP, FLATMAP, FILTER ( Identical API )
4.  rdd.map, rdd.flatMap, rdd.filter <-> typedpipe.map, typedpipe.flatMap, typedpipe.filter

SIZE
5. rdd.count <-> typedpipe.groupAll.size

LIST2RDD ( Convert local Scala list to an RDD )
6. sc.parallelize( list ) <-> TypedPipe.from(list).flatMap{ x=> x } // need this additional flatmap in Scalding

INNER JOIN
7. new PairRDDFunctions(brdd.groupBy{ x=> x.key }).join(ardd.groupBy{ x=> x.key})
<->
pipe1.groupBy( x=> x.key }.join(pipe2.groupBy{ x=> x.key })

LEFT JOIN
8.new PairRDDFunctions(brdd.groupBy{ x=> x.key }).leftOuterjoin(ardd.groupBy{ x=> x.key})
<->
pipe1.groupBy( x=> x.key }.leftJoin(pipe2.groupBy{ x=> x.key })

CARTESIAN PRODUCT
9. ardd.cartesian(brdd) <-> pipe1.cross(pipe2)

CATAMORPHISM
10. ardd.fold(init){(a,b) => op(a,b) }  <-> pipe1.groupAll.foldLeft(init){ (a,b) => op(a,b) }

DISTINCT
11. rdd.distinct <-> pipe.groupAll.distinct

</pre>

