# Installing-spark-standalone-to-a-cluster-manually

## About
Next to running Spark on the YARN cluster manager, Spark itself provides a standalone deploy mode. We can launch a standalone cluster manually  by starting a master and workers daemons by hand. This approach makes testing easier by running these daemons on a single machine.
I'll walk you through launching the cluster, as well as connecting an app to the cluster, launching the app, where to view the monitoring and logging. 

### Start a cluster
First we need to start spark standalone master serve
`{apache spark directory/libexec}/sbin/start-master.sh`

Now open  http://localhost:8080/ in the browser, and copy the spark url (spark://HOSTNAME:PORT)

Second we can start one or more workers and connect them to the master
`{apache spark directory/libexec}/sbin/start-slave.sh <master-spark-URL>`
`{apache spark directory/libexec}/sbin/start-slaves.sh <master-spark-URL>`

If you look again at th spark master’s web UI (http://localhost:8080). You'll see the new node/s (worker/s) listed there.

### Connecting an app to the cluster
Either run the application on the Spark cluster by passing the spark://HOSTNAME:PORT to the SparkContext constructor.

Or to run an interactive Spark shell against the cluster, run the following command:

`spark-shell --master spark://HOSTNAME:PORT`

### Launching an app 
To launch a compiled Spark application to a cluster is by using the `spark-submit` script.  
  `spark-submit --class <main-class> \
  --master <master-url> \
  --deploy-mode <deploy-mode> \
  --conf <key>=<value> \
  ... # other options
  <application-jar> \
  [application-arguments]`
 
--class: your application entry point
--master: The master URL for the cluster (e.g.`local` for one worker `local[k]` for k workers)
--deploy-mode: `default: client`
--conf: Spark configuration property in key=value format (e.g to enable history, --conf spark.eventLog.enabled=true).
application-jar: a bundled jar for all of your application's dependencies.
application-arguments: Arguments passed to the main method in the main class (e.g. could be files' path as input and output).

### Monitoring and Logging
Spark’s standalone mode offers a user interface to monitor the cluster called 'spark server history'
#### setup server history 
The Spark History server allows us to review Spark application metrics after the application has been completed.
1. Create ~/tmp/spark-events 
2. Create a file spark-defaults.conf under the path [SPARK_PATH]/libexec/conf )
3. Add these lines in the spark-defaults.conf file:
  `spark.eventLog.enabled true`
  `spark.eventLog.dir file:/Users/<USER_NAME>/tmp/spark-events`
  `spark.history.fs.logDirectory file:/Users/<USER_NAME>/tmp/spark-events`

4. Start the history server :` [SPARK_PATH]/sbin/start-history-server.sh`
5. You can see the history http://localhost:18080


## Refernces:
https://spark.apache.org/docs/latest/spark-standalone.html#cluster-launch-scripts

https://spark.apache.org/docs/latest/submitting-applications.html

https://spark.apache.org/docs/latest/monitoring.html
