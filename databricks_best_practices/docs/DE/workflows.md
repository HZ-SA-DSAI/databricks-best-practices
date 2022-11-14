# Workflows

1. [Use shared job clusters](https://docs.databricks.com/workflows/jobs/jobs.html#use-shared-job-clusters)
    1. To optimize resource usage with jobs that orchestrate multiple tasks, use shared job clusters. 
    2. A shared job cluster allows multiple tasks in the same job run to reuse the cluster.
    3. You can use a single job cluster to run all tasks that are part of the job, or multiple job clusters optimized for specific workloads.
    4. A shared job cluster is scoped to a single job run, and cannot be used by other jobs or runs of the same job.
    5. Libraries cannot be declared in a shared job cluster configuration. You must add dependent libraries in task settings.
2. In production, Databricks recommends using new shared or task scoped clusters so that each job or task runs in a fully isolated environment.
3. When you run a task on a new cluster, the task is treated as a data engineering (task) workload, subject to the task workload pricing. When you run a task on an existing all-purpose cluster, the task is treated as a data analytics (all-purpose) workload, subject to all-purpose workload pricing.
4. Existing all-purpose clusters work best for tasks such as updating dashboards at regular intervals.
5. To decrease new job cluster start time, create a [pool](https://docs.databricks.com/clusters/instance-pools/cluster-instance-pool.html#cluster-instance-pool) and configure the job’s cluster to use the pool.
6. To take advantage of automatic [availability zones](https://docs.databricks.com/clusters/configure.html#availability-zones) (Auto-AZ), you must enable it with the Clusters API, setting awsattributes.zone_id = "auto". See Availability zones.
7. Total notebook cell output (the combined output of all notebook cells) is subject to a 20MB size limit. Additionally, individual cell output is subject to an 8MB size limit. If total cell output exceeds 20MB in size, or if the output of an individual cell is larger than 8MB, the run is canceled and marked as failed.

    If you need help finding cells near or beyond the limit, run the notebook against an all-purpose cluster and use this [notebook autosave technique](https://kb.databricks.com/notebooks/notebook-autosave.html?_ga=2.111477389.868976919.1668462910-917306584.1665418964).

8. Streaming tasks
    1. Spark Streaming jobs should never have maximum concurrent runs set to greater than 1.
    2. Streaming jobs should be set to run using the cron expression "* * * * * ?" (every minute).
    3. Since a streaming task runs continuously, it should always be the final task in a job.
9. JAR jobs
    1. Job output, such as log output emitted to stdout, is subject to a 20MB size limit. If the total output has a larger size, the run is canceled and marked as failed.
        1. To avoid encountering this limit, you can prevent stdout from being returned from the driver to Databricks by setting the spark.databricks.driver.disableScalaOutput Spark configuration to true.
        2. Setting this flag is recommended only for job clusters for JAR jobs because it will disable notebook results.
    2. JAR job programs must use the shared SparkContext API to get the SparkContext. 
        1. Do not call SparkContext.stop().
        2. Do not call System.exit(0) or sc.stop() at the end of your Main program. This can cause undefined behavior.
    3. Use try-finally blocks for job clean up
        1. jobBody() which contains the main part of the job.
        2. jobCleanup() which has to be executed after jobBody() whether that function succeeded or returned an exception.
        3. As an example, jobBody() may create tables, and you can use jobCleanup() to drop these tables.
        4. You should not try to clean up using sys.addShutdownHook(jobCleanup)
    4. You pass parameters to JAR jobs with a JSON string array. 
        See the spark_jar_task object in the request body passed to the [Create a new job](https://docs.databricks.com/dev-tools/api/latest/jobs.html) operation (POST /jobs/create) in the Jobs API. To access these parameters, inspect the String array passed into your main function.
10. Library dependencies
    1. The Spark driver has certain library dependencies that cannot be overridden. These libraries take priority over any of your libraries that conflict with them. [See here to get a full list of driver library dependencies. ](https://docs.databricks.com/workflows/jobs/jobs.html#library-dependencies)
    2. A good rule of thumb when dealing with library dependencies while creating JARs for jobs is to list Spark and Hadoop as provided dependencies. On Maven, add Spark and Hadoop as provided dependencies. [See here for more info. ](https://docs.databricks.com/workflows/jobs/jobs.html#manage-library-dependencies)