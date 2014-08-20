# ERROR OneForOneStrategy

If you enable checkpointing in Spark Streaming, then objects
used in a function called in forEachRDD should be Serializable.
Otherwise, there will be an "ERROR OneForOneStrategy: ... java.io.NotSerializableException:

```java
JavaStreamingContext jssc = new JavaStreamingContext(sc, INTERVAL);

// This enables checkpointing.
jssc.checkpoint("/tmp/checkpoint_test");

JavaDStream<String> dStream = jssc.socketTextStream("localhost", 9999);

NotSerializable notSerializable = new NotSerializable();
dStream.foreachRDD(rdd -> {
      if (rdd.count() == 0) {
        return null;
      }
      String first = rdd.first();

      notSerializable.doSomething(first);
      return null;
    }
);

// This does not work!!!!
```

This code will run if you make one of these changes to it:
* Turn off checkpointing by removing the `jssc.checkpoint` line.
* Make the object being used Serializable.
* Declare NotSerializable inside the forEachRDD function, so the following code sample would be fine:

```java
JavaStreamingContext jssc = new JavaStreamingContext(sc, INTERVAL);

jssc.checkpoint("/tmp/checkpoint_test");

JavaDStream<String> dStream = jssc.socketTextStream("localhost", 9999);

dStream.foreachRDD(rdd -> {
      if (rdd.count() == 0) {
        return null;
      }
      String first = rdd.first();
      NotSerializable notSerializable = new NotSerializable();
      notSerializable.doSomething(first);
      return null;
    }
);

// This code snippet is fine since the NotSerializable object
// is declared and only used within the forEachRDD function.
```
