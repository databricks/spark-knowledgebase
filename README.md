# Databricks Spark Knowledge Base

The contents contained here is also published in [Gitbook format](http://databricks.gitbooks.io/databricks-spark-knowledge-base/).

* [Best Practices](best_practices/README.md)
   * [Avoid GroupByKey](best_practices/prefer_reducebykey_over_groupbykey.md)
   * [Don't copy all elements of a large RDD to the driver](best_practices/dont_call_collect_on_a_very_large_rdd.md)
   * [Gracefully Dealing with Bad Input Data](best_practices/dealing_with_bad_data.md)
* [General Troubleshooting](troubleshooting/README.md)
   * [Job aborted due to stage failure: Task not serializable: ](troubleshooting/javaionotserializableexception.md)
   * [Missing Dependencies in Jar Files](troubleshooting/missing_dependencies_in_jar_files.md)
   * [Error running start-all.sh - Connection refused](troubleshooting/port_22_connection_refused.md)
   * [Network connectivity issues between Spark components](troubleshooting/connectivity_issues.md)
* [Performance & Optimization](performance_optimization/README.md)
   * [How Many Partitions Does An RDD Have?](performance_optimization/how_many_partitions_does_an_rdd_have.md)
   * [Data Locality](performance_optimization/data_locality.md)
* [Spark Streaming](spark_streaming/README.md)
   * [ERROR OneForOneStrategy](spark_streaming/error_oneforonestrategy.md)

This content is covered by the license specified [here](LICENSE).
