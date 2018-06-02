
1. 读入fasta格式数据：
第一次：
		
		hadoop@Master:~/xubo/project/load/loadfastqFromHDFSfastaAndCount$ ./load.sh 
		start:
		1
		run time:25101 ms
		*************end*************
		hadoop@Master:~/xubo/project/load/loadfastqFromHDFSfastaAndCount$ mv load.sh loadGRCH38chr14.sh
		hadoop@Master:~/xubo/project/load/loadfastqFromHDFSfastaAndCount$ cp loadGRCH38chr14.sh loadGRCH38.sh
		hadoop@Master:~/xubo/project/load/loadfastqFromHDFSfastaAndCount$ vi loadGRCH38.sh 
		hadoop@Master:~/xubo/project/load/loadfastqFromHDFSfastaAndCount$ ./loadGRCH38.sh 
		start:
		[Stage 2:======================================================>  (24 + 1) / 25]16/06/08 13:47:37 ERROR TaskSchedulerImpl: Lost executor 1 on 219.219.220.215: remote Rpc client disassociated
		456                                                                             
		run time:585513 ms
		*************end*************
第二次：

		hadoop@Master:~/xubo/project/load/loadfastaFromHDFSfastaAndCount$ ./loadGRCH38chr14.sh 
		start:
		1
		run time:30775 ms
		*************end*************
		hadoop@Master:~/xubo/project/load/loadfastaFromHDFSfastaAndCount$ ./loadGRCH38.sh 
		start:
		456                                                                             
		run time:262677 ms
		*************end*************
		16/06/08 14:01:59 WARN QueuedThreadPool: 8 threads could not be stopped
		16/06/08 14:02:04 WARN QueuedThreadPool: 1 threads could not be stopped

2.读入adam：


读取方法不对

	//    val rdd = sc.loadParquetContigFragments(args(0))
   
解决办法：

    val rdd = sc.loadSequence(args(0))

运行记录：

	hadoop@Master:~/xubo/project/load/loadfastaFromHDFSAdamAndCount$ ./loadGRCH38chr14.sh 
	start:
	1                                                                               
	run time:25802 ms
	*************end*************
	Jun 8, 2016 2:08:11 PM INFO: org.apache.parquet.hadoop.ParquetInputFormat: Total input paths to process : 1
	hadoop@Master:~/xubo/project/load/loadfastaFromHDFSAdamAndCount$ ./loadGRCH38.sh 
	start:
	456                                                                             
	run time:40620 ms
	*************end*************
	Jun 8, 2016 2:08:56 PM INFO: org.apache.parquet.hadoop.ParquetInputFormat: Total input paths to process : 25

报错记录请见spark问题1


3.文件大小：
![](http://i.imgur.com/HUFeZ0f.png)
![](http://i.imgur.com/1ddpzGG.png)
![](http://i.imgur.com/FnEA0Fx.png)


4.代码：

	package org.gcdss.cli.load
	
	import org.apache.spark.{SparkConf, SparkContext}
	import org.bdgenomics.adam.rdd.ADAMContext._
	
	//import org.bdgenomics.avocado.AvocadoFunSuite
	
	object loadfastaFromHDFSAdamAndCount {
	
	
	  def main(args: Array[String]) {
	    println("start:")
	    var conf = new SparkConf().setAppName(this.getClass().getSimpleName().filter(!_.equals('$'))).setMaster("spark://219.219.220.149:7077")
	    //    var conf = new SparkConf().setAppName(this.getClass().getSimpleName().filter(!_.equals('$'))).setMaster("local[4]")
	    val sc = new SparkContext(conf)
	    val startTime = System.currentTimeMillis()
	
	    //    val path = "hdfs://219.219.220.149:9000/xubo/ref/GRCH38Index/GCA_000001405.15_GRCh38_full_analysis_set.fna"
	    //    val path = "hdfs://219.219.220.149:9000/xubo/ref/GRCH38chr14/GRCH38chr14.fasta"
	    //    val rdd = sc.loadFasta(path, 1000000000L)
	//    val rdd = sc.loadParquetContigFragments(args(0))
	    val rdd = sc.loadSequence(args(0))
	
	    println(rdd.count())
	    val saveTime = System.currentTimeMillis()
	    println("run time:" + (saveTime - startTime) + " ms")
	    println("*************end*************")
	    sc.stop()
	
	  }
	
	}
