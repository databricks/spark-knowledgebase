# Job aborted due to stage failure: Task not serializable:

If you see this error:
```
org.apache.spark.SparkException: Job aborted due to stage failure: Task not serializable: java.io.NotSerializableException: ...
```

The above error can be triggered when you intialize a variable on the driver (master), but then try to use it on one of the workers.  In that case, Spark
Streaming will try to serialize the object to send it over to the worker, and fail if the object is not serializable.  Consider the following code snippet:

```java
NotSerializable notSerializable = new NotSerializable();
JavaRDD<String> rdd = sc.textFile("/tmp/myfile");

rdd.map(s -> notSerializable.doSomething(s)).collect();
```

This will trigger that error.  Here are some ideas to fix this error:

* Serializable the class
* Declare the instance only within the lambda function passed in map.
* Make the NotSerializable object as a static and create it once per machine.
* Call rdd.forEachPartition and create the NotSerializable object in there like this:

```java
rdd.forEachPartition(iter -> {
  NotSerializable notSerializable = new NotSerializable();

  // ...Now process iter
});
```

