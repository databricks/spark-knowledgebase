# Don't copy all elements of a large RDD to the driver.

If your RDD is so large that all of it's elements won't fit in memory on the drive machine, don't do this:
```
val values = myVeryLargeRDD.collect()
```
Collect will attempt to copy every single element in the RDD onto the single driver program, and then run out of memory and crash.

Instead, you can make sure the number of elements you return is capped by calling `take` or `takeSample`, or perhaps filtering or sampling your RDD.

Similarly, be cautious of these other actions as well unless you are sure your dataset size is small enough to fit in memory:

* `countByKey`
* `countByValue`
* `collectAsMap`

If you really do need every one of these values of the RDD and the data is too big to fit into memory, you can write out the RDD to files or export the RDD to a database that is large enough to hold all the data.
