# Network connectivity issues between Spark components

Network connectivity issues between Spark components can lead to a variety of warnings / errors:

- **SparkContext <-> Spark Standalone Master**:

   If the SparkContext cannot connect to a Spark standalone master, then the driver may display errors like

   ```
   ERROR AppClient$ClientActor: All masters are unresponsive! Giving up.
   ERROR SparkDeploySchedulerBackend: Spark cluster looks dead, giving up.
   ERROR TaskSchedulerImpl: Exiting due to error from cluster scheduler: Spark cluster looks down
   ```

   If the driver is able to connect to the master but the master is unable to communicate back to the driver, then the Master's logs may record multiple attempts to connect even though the driver will report that it could not connect:

   ```
   INFO Master: Registering app SparkPi
   INFO Master: Registered app SparkPi with ID app-XXX-0000
   INFO: Master: Removing app app-app-XXX-0000
   [...]
   INFO Master: Registering app SparkPi
   INFO Master: Registered app SparkPi with ID app-YYY-0000
   INFO: Master: Removing app app-YYY-0000
   [...]
   ```

   In this case, the master reports that it has successfully registered an application, but if the acknowledgment of this registration fails to be received by the driver, then the driver will automatically make several attempts to re-connect before eventually giving up and failing.  As a result, the master web UI may report multiple failed applications even though only a single SparkContext was created.

## Recomendations

If you are experiencing any of the errors described above:

- Check that the workers and drivers are configured to connect to the Spark master on the exact address listed in the Spark master web UI / logs.
- Set `SPARK_LOCAL_IP` to a cluster-addressable hostname for the driver, master, and worker processes.



## Configurations that determine hostname/port binding:

This section describes configurations that determine which network interfaces and ports Spark components will bind to.

In each section, the configurations are listed in decreasing order of precedence, with the final entry being the default configuration if none of the previous configurations were supplied.

### SparkContext actor system:

**Hostname:**
- The `spark.driver.host` configuration property.
- If the `SPARK_LOCAL_IP` environment variable is set to a hostname, then this hostname will be used.  If `SPARK_LOCAL_IP` is set to an IP address, it will be resolved to a hostname.
- The IP address of the interface returned from Java's `InetAddress.getLocalHost` method.

**Port:**
- The `spark.driver.port` configuration property.
- An ephemeral port chosen by the OS.

### Spark Standalone Master / Worker actor systems:

**Hostname:**

- The `--host`, or `-h` options (or the deprecated `--ip` or `-i` options) when launching the `Master` or `Worker` process.
- The `SPARK_MASTER_HOST` environment variable (only applies to `Master`).
- If the `SPARK_LOCAL_IP` environment variable is set to a hostname, then this hostname will be used.  If `SPARK_LOCAL_IP` is set to an IP address, it will be resolved to a hostname.
- The IP address of the interface returned from Java's `InetAddress.getLocalHost` method.

**Port:**
- The `--port`, or `-p` options when launching the `Master` or `Worker` process.
- The `SPARK_MASTER_PORT` or `SPARK_WORKER_PORT` environment variables (only apply to `Master` and `Worker`, respectively).
- An ephemeral port chosen by the OS.
