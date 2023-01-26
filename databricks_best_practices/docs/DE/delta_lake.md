***
# Delta Lake

**Updated on 01/25/2023**

*See below for original doc on docs.databricks.com*

*[Best practices: Delta Lake](https://docs.databricks.com/delta/best-practices.html#best-practices-delta-lake)*

*[When to partition tables on Databricks](https://docs.databricks.com/tables/partitions.html#when-to-partition-tables-on-databricks)*

1. [Provide data location hints](https://docs.databricks.com/delta/best-practices.html#provide-data-location-hints)
    
    If you expect a column to be commonly used in query predicates and if that column has high cardinality (that is, a large number of distinct values), then use Z-ORDER BY

2. [Compact files](https://docs.databricks.com/delta/best-practices.html#compact-files)
    
    Compact a table using the OPTIMIZE command to rewrite large number of small files into a smaller number of larger files on a regular basis.

3. [Carefully consider how to replace the content or schema of a table](https://docs.databricks.com/delta/best-practices.html#replace-the-content-or-schema-of-a-table)
    1. If you don’t need to change the table schema, you can [delete](https://docs.databricks.com/delta/tutorial.html#delete) data from a Delta table and insert your new data, or [update](https://docs.databricks.com/delta/tutorial.html#update) the table to fix the incorrect values.
    2. If you want to change the table schema, you can replace the whole table atomically. 
    3. If you want to delete old files to save storage cost after overwriting the table, you can use VACUUM to delete them.
4. [Avoid Spark caching](https://docs.databricks.com/delta/best-practices.html#spark-caching)
5. [Understand the differences between Delta Lake and Parquet on Apache Spark](https://docs.databricks.com/delta/best-practices.html#differences-between-delta-lake-and-parquet-on-apache-spark). For Delta Lake:
    1. No need to call REFRESH TABLE manually after changes.
    2. No need to run ALTER TABLE [ADD|DROP] PARTITION or MSCK.
    3. Reading partitions directly is not necessary.
    4. Do not directly modify, add, or delete Parquet data files in a Delta table.
6. [Options to improve performance for Delta Lake merge](https://docs.databricks.com/delta/best-practices.html#improve-performance-for-delta-lake-merge)
    1. Reduce the search space by adding known constraints in the match condition.
    2. Compact small files into larger files to improve read throughput.
    3. Control the shuffle partitions for writes.
    4. [Enable optimized writes.](https://docs.databricks.com/optimizations/auto-optimize.html#how-auto-optimize-works)
    5. [Tune file sizes in table.](https://docs.databricks.com/delta/tune-file-size.html)
    6. [Low Shuffle Merge.](https://docs.databricks.com/optimizations/low-shuffle-merge.html)
7. [Manage data recency](https://docs.databricks.com/delta/best-practices.html#manage-data-recency)
    
    You can configure how stale your table data is by setting the Spark session configuration spark.databricks.delta.stalenessLimit with a time string value. 

8. [Enhanced checkpoints for low-latency queries](https://docs.databricks.com/delta/best-practices.html#enhanced-checkpoints-for-low-latency-queries)

    Delta Lake writes checkpoints as an aggregate state of a Delta table at an optimized frequency.These checkpoints serve as the starting point to compute the latest state of the table.

    Delta Lake checkpoints are different than Structured Streaming checkpoints.

9. [Manage column-level statistics in checkpoints](https://docs.databricks.com/delta/best-practices.html#manage-column-level-statistics-in-checkpoints)

    You manage how statistics are written in checkpoints using the table properties delta.checkpoint.writeStatsAsJson and delta.checkpoint.writeStatsAsStruct. If both table properties are false, Delta Lake cannot perform data skipping.

10. [Enable enhanced checkpoints for Structured Streaming queries](https://docs.databricks.com/delta/best-practices.html#enable-enhanced-checkpoints-for-structured-streaming-queries)

    If your Structured Streaming workloads don’t have low latency requirements (subminute latencies), you can enable enhanced checkpoints. 

    Databricks does not recommend disabling enhanced checkpoints as no statistics will be collected or written for data skipping purposes. 

11. [Best Practices on Partitioning](https://docs.databricks.com/tables/partitions.html#when-to-partition-tables-on-databricks)

    1. Because of built-in features and optimizations, most tables with less than 1 TB of data do not require partitions.
    2. Databricks recommends all partitions contain at least a gigabyte of data. Tables with fewer, larger partitions tend to outperform tables with many smaller partitions.
    3. By using Delta Lake and Databricks Runtime 11.2 or above, unpartitioned tables you create benefit automatically from [ingestion time clustering](https://www.databricks.com/blog/2022/11/18/introducing-ingestion-time-clustering-dbr-112.html). Ingestion time provides similar query benefits to partitioning strategies based on datetime fields without any need to optimize or tune your data.

        To maintain ingestion time clustering when you perform a large number of modifications using UPDATE or MERGE statements on a table, Databricks recommends running OPTIMIZE with ZORDER BY using a column that matches the ingestion order. For instance, this could be a column containing an event timestamp or a creation date.

        Some experienced users of Apache Spark and Delta Lake might be able to design and implement a pattern that provides better performance than ingestion time clustering. Implementing a bad partitioning stategy can have very negative repercussions on downstream performance and might require a full rewrite of data to fix. Databricks recommends that most users use default settings to avoid introducing expensive inefficiencies.

    4. You can use Z-order indexes alongside partitions to speed up queries on large datasets. However, most tables can leverage ingestion time clustering to avoid needing to worry about Z-order and partition tuning.

        1. Z-order works in tandem with the OPTIMIZE command. You cannot combine files across partition boundaries, and so Z-order clustering can only occur within a partition. For unpartitioned tables, files can be combined across the entire table.
        2. Partitioning works well only for low or known cardinality fields (for example, date fields or physical locations), but not for fields with high cardinality such as timestamps. Z-order works for all fields, including high cardinality fields and fields that may grow infinitely (for example, timestamps or the customer ID in a transactions or orders table).
        3. You cannot Z-order on fields used for partitioning.

    4. Differences regarding partitions between Delta Lake and other data lakes
        1. Transactions are not defined by partition boundaries. Delta Lake ensures [ACID](https://docs.databricks.com/lakehouse/acid.html) through transaction logs, so you do not need to separate a batch of data by a partition to ensure atomic discovery.
        2. Databricks compute clusters do not have data locality tied to physical media. Data ingested into the lakehouse is stored in cloud object storage. While data is cached to local disk storage during data processing, Databricks uses file-based statistics to identify the minimal amount of data for parallel loading.