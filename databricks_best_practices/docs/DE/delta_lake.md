1. [Provide data location hints](https://docs.databricks.com/delta/best-practices.html#provide-data-location-hints)
    
    If you expect a column to be commonly used in query predicates and if that column has high cardinality (that is, a large number of distinct values), then use Z-ORDER BY

2. [Compact files](https://docs.databricks.com/delta/best-practices.html#compact-files)
    
    Compact a table using the OPTIMIZE command to rewrite large number of small files into a smaller number of larger files on a regular basis.

3. [Carefully consider how to replacee the content or schema of a table](https://docs.databricks.com/delta/best-practices.html#replace-the-content-or-schema-of-a-table)
    1. If you don’t need to change the table schema, you can delete data from a Delta table and insert your new data, or update the table to fix the incorrect values.
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
9. [Manage column-level statistics in checkpoints](https://docs.databricks.com/delta/best-practices.html#manage-column-level-statistics-in-checkpoints)
10. [Enable enhanced checkpoints for Structured Streaming queries](https://docs.databricks.com/delta/best-practices.html#enable-enhanced-checkpoints-for-structured-streaming-queries)