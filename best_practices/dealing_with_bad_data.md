# Gracefully Dealing with Bad Input Data

When dealing with vast amounts of data, a common problem is that a small amount of the data is malformed or corrupt.  Using a ```filter``` transformation, you can easily discard bad inputs, or use a ```map``` transformation if it's possible to fix the bad input.  Or perhaps the best option is to use a ```flatMap``` function where you can try fixing the input but fall back to discarding the input if you can't.


Let's consider the json strings below as input:
```
input_rdd = sc.parallelize(["{\"value\": 1}",  # Good
                            "bad_json",  # Bad
                            "{\"value\": 2}",  # Good
                            "{\"value\": 3"  # Missing an ending brace.
                            ])
```
If we tried to input this set of json strings to a sqlContext, it would clearly fail due to the malformed input's.
```
sqlContext.jsonRDD(input_rdd).registerTempTable("valueTable")
# The above command will throw an error.
```

Instead, let's try fixing the input with this python function:
```
def try_correct_json(json_string):
  try:
    # First check if the json is okay.
    json.loads(json_string)
    return [json_string]
  except ValueError:
    try:
      # If not, try correcting it by adding a ending brace.
      try_to_correct_json = json_string + "}"
      json.loads(try_to_correct_json)
      return [try_to_correct_json]
    except ValueError:
      # The malformed json input can't be recovered, drop this input.
      return []
```

Now, we can apply that function to fix our input and try again.  This time we will succeed to read in three inputs:
```
corrected_input_rdd = input_rdd.flatMap(try_correct_json)
sqlContext.jsonRDD(corrected_input_rdd).registerTempTable("valueTable")
sqlContext.sql("select * from valueTable").collect()
# Returns [Row(value=1), Row(value=2), Row(value=3)]
```

