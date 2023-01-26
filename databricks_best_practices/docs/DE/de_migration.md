***
# Migrate ETL jobs to Databricks

**Updated on 01/25/2023**

*See below for original doc on docs.databricks.com*

*[Migrate ETL jobs to Databricks](https://docs.databricks.com/migration/index.html#migrate-etl-jobs-to-databricks)*

1. You can migrate Apache Spark jobs used to extract, transform, and load data from on-prem or cloud-native implementations to Databricks with just a few steps. See [Adapt your exisiting Apache Spark code for Databricks](https://docs.databricks.com/migration/spark.html).

2. If your ETL workloads are written in SQL or Hive, you can migrate to Databricks with minimal refactoring. 

3. [Apache Spark](https://docs.databricks.com/migration/spark.html)

    3. [Change parquet to delta](https://docs.databricks.com/migration/spark.html#change-parquet-to-delta)

        1. Databricks recommends using Delta Lake instead of Parquet or ORC when writing data. 
        2. Databricks has optimized many features for efficiency when interacting with tables backed by Delta Lake, and upgrading data and code form Parquet to Delta Lake only takes a few steps. See [Migrate a Parquet data lake to Delta Lake.](https://docs.databricks.com/migration/parquet-to-delta-lake.html)

    4. [Recompile Apache Spark code with Databricks Runtime compatible libraries](https://docs.databricks.com/migration/spark.html#recompile-apache-spark-code-with-databricks-runtime-compatible-libraries)

        You can install additional libraries to your compute as required, but whenenever possible, Databricks recommends using library versions packaged in the Databricks Runtime which are tested for compatability.

    5. [Remove SparkSession creation commands](https://docs.databricks.com/migration/spark.html#remove-sparksession-creation-commands)

        Databricks automatically creates a SparkContext for each compute cluster, and creates an isolated SparkSession for each notebook or job executed against the cluster. You can maintain the ability to compile and test code locally and then deploy to Databricks by upgrading these commands to use SparkSession.builder().getOrCreate().

    6. [Remove terminal script commands](https://docs.databricks.com/migration/spark.html#remove-terminal-script-commands)

        Databricks automatically terminates and cleans up jobs as they reach completion. 

        Databricks also automatically terminates and cleans up Structured Streaming workloads on run termination

    7. [Trust Databricks to configure your cluster](https://docs.databricks.com/migration/spark.html#trust-databricks-to-configure-your-cluster)

        Databricks configures all of the settings for the driver and executors in your compute cluster automatically to maximize resiliency and resource usage. Providing custom configurations for the executors or JVM can result in reduced performance. Databricks recommends only setting Spark configurations that are necessary for controlling type handling or functions so that logic remains consistent.

4. [Hive pipelines](https://docs.databricks.com/migration/etl.html#can-you-run-hive-pipelines-on-databricks)

    Most Hive workloads can run on Databricks with minimal refactoring. The version of Spark SQL supported by Databricks Runtime allows many HiveQL constructs. [See Apache Hive compatibility](https://docs.databricks.com/sql/language-manual/sql-ref-hive-compatibility.html). Databricks includes a Hive metastore by default. 

5. [SQL ETL pipelines:](https://docs.databricks.com/migration/etl.html#can-you-run-sql-etl-pipelines-on-databricks)

    1. Migrating SQL workloads from other systems to Databricks usually requires very little refactoring, depending on the extent to which system specific protocols were used in the source code. Databricks uses Delta Lake as the default table format in Databricks Runtime 8.4 and above, so tables are created with [transactional guarantees](https://docs.databricks.com/lakehouse/acid.html) by default.
    2. Spark SQL is mostly ANSI-compliant, but some differences in behavior might exist. [See How is the Databricks Lakehouse different than a data warehouse?.](https://docs.databricks.com/migration/warehouse-to-lakehouse.html#differences)
    3. Because data systems tend to configure access to external data differently, much of the work refactoring SQL ETL pipelines might be configuring access to these data sources and then updating your logic to use these new connections. Databricks provides options for connecting to [many data sources for ingestion.](https://docs.databricks.com/ingestion/index.html)

6. [dbt ETL pipelines](https://docs.databricks.com/migration/etl.html#can-you-run-dbt-etl-pipelines-on-databricks):

    1. Databricks provides a [native integration with dbt](https://docs.databricks.com/partners/prep/dbt-cloud.html), allowing you to leverage existing dbt scripts with very little refactoring.

    2. Delta Live Tables provides an optimized Databricks-native declarative SQL syntax for creating, testing, and deploying pipelines. While you can leverage dbt on Databricks, a light refactor of code to Delta Live Tables might lower your total cost to operate your pipelines on Databricks. [See Delta Live Tables introduction.](https://docs.databricks.com/workflows/delta-live-tables/index.html)

7. [Serverless cloud functions](https://docs.databricks.com/migration/etl.html#can-you-migrate-serverless-cloud-functions-to-databricks)

    1. The extensibility and versatility of custom serverless cloud functions makes it difficult to provide a common recommendation, but one of the most common use cases for these functions is waiting for files or data to appear in a location or message queue and then performing some action as a result. While Databricks does not support complex logic for triggering workloads based on cloud conditions, you can use Structured Streaming in conjunction with [workflows](https://docs.databricks.com/workflows/index.html) to process data incrementally.
    2. Use [Auto Loader](https://docs.databricks.com/ingestion/auto-loader/index.html) for optimized data ingestion from cloud object storage. Structured Streaming can process data from [streaming sources](https://docs.databricks.com/external-data/index.html#streaming-sources) in near-real time.

8. ETL pipelines defined in languages other than SQL, Apache Spark, or Hive might need to be heavily refactored before running on Databricks.