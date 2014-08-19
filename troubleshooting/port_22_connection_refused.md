# Error running start-all.sh Connection refused

If you are on a Mac and run into the following error when running start-all.sh:

```
% sh start-all.sh
starting org.apache.spark.deploy.master.Master, logging to ...
localhost: ssh: connect to host localhost port 22: Connection refused
```

You need to enable "Remote Login" for your machine.  From System Preferences, select Sharing, and then turn on Remote Login.





