***
# Optimization Recommendations

**Updated on 01/24/2023**

*See below for original doc on docs.databricks.com*

*[Optimization recommendations on Databricks](https://docs.databricks.com/optimizations/index.html)*

1. Use the latest Databricks Runtime to leverage the newest performance enhancements. *All behaviors documented below are enabled by default in Databricks Runtime 10.4 LTS and above.*

    1. [Disk caching](https://docs.databricks.com/optimizations/disk-cache.html) accelerates repeated reads against Parquet data files by loading data to disk volumes attached to compute clusters.

        1. The recommended (and easiest) way to use disk caching is to choose a worker type with SSD volumes when you configure your cluster. Such workers are enabled and configured for disk caching.
        
        2. Databricks recommends that you choose cache-accelerated worker instance types for your clusters. Such instances are automatically configured optimally for the disk cache.

        3. When a worker is decommissioned, the Spark cache stored on that worker is lost. So if autoscaling is enabled, there is some instability with the cache. Spark would then need to reread missing partitions from source as needed.

    2. [Dynamic file pruning](https://docs.databricks.com/optimizations/dynamic-file-pruning.html) improves query performance by skipping directories that do not contain data files that match query predicates.

        Dynamic file pruning is especially efficient for non-partitioned tables, or for joins on non-partitioned columns. The performance impact of dynamic file pruning is often correlated to the clustering of data so consider using Z-Ordering to maximize the benefit.

    3. [Low shuffle merge](https://docs.databricks.com/optimizations/low-shuffle-merge.html) reduces the number of data files rewritten by MERGE operations and reduces the need to recaculate ZORDER clusters.
    4. Apache Spark 3.0 introduced [adaptive query execution](https://docs.databricks.com/optimizations/aqe.html), which provides enhanced performance for many operations.

        AQE applies to all queries that are non-streaming and contain at least one exchange (usually when there’s a join, aggregate, or window), one sub-query, or both. Not all AQE-applied queries are necessarily re-optimized.

2. You can [clone](https://docs.databricks.com/optimizations/clone.html) tables on Databricks to make deep or shallow copies of source datasets.

3. The [cost-based optimizer](https://docs.databricks.com/optimizations/cbo.html) accelerates query performance by leveraging table statistics.

    To get the full benefit of the CBO, it is important to collect both column statistics and table statistics. Statistics can be collected using the ANALYZE TABLE command. To maintain the statistics up-to-date, run ANALYZE TABLE after writing to the table.

4. You can [auto optimize](https://docs.databricks.com/optimizations/auto-optimize.html) Delta tables using optimized writes and automatic file compaction

    1. Auto optimize adds latency overhead to write operations but accelerates read operations.
    2. Auto optimize is particularly useful in the following scenarios:
        1. Streaming use cases where latency in the order of minutes is acceptable
        2. MERGE INTO is the preferred method of writing into Delta Lake
        3. CREATE TABLE AS SELECT or INSERT INTO are commonly used operations

5. You can use Spark SQL to interact with [semi-structured JSON](https://docs.databricks.com/optimizations/semi-structured.html) data without parsing strings.

    This feature lets you read semi-structured data without flattening the files. However, for optimal read query performance Databricks recommends that you extract nested columns with the correct data types.

6. [Higher order functions](https://docs.databricks.com/optimizations/higher-order-lambda-functions.html) provide built-in, optimized performance for many operations that do not have common Spark operators. Higher order functions provide a performance benefit over user defined functions.

7. Databricks provides a number of built-in operators and special syntax for working with [complex data types](https://docs.databricks.com/optimizations/complex-types.html), including arrays, structs, and JSON strings. While working with nested data types, Databricks optimizes certain transformations out-of-the-box. 

8. You can manually tune settings for joins that include [ranges](https://docs.databricks.com/optimizations/range-join.html) or contain data with substanial [skew](https://docs.databricks.com/optimizations/skew-join.html).

    1. The range join optimization support in Databricks Runtime can bring orders of magnitude improvement in query performance, but requires careful manual tuning.
    2. It’s likely that data skew is affecting a query if a query appears to be stuck finishing very few tasks (for example, the last 3 tasks out of 200). To ameliorate skew, Delta Lake on Databricks SQL accepts skew hints in queries. With the information from a skew hint, Databricks Runtime can construct a better query plan, one that does not suffer from data skew.

9. Databricks provides a write serializable isolation guarantee by default; changing the [isolation level](https://docs.databricks.com/optimizations/isolation-level.html) to serializable can reduce throughput for concurrent operations, but might be necessary when read serializability is required.

10. You can use [bloom filter indexes](https://docs.databricks.com/optimizations/bloom-filters.html) to reduce the likelihood of scanning data files that don’t contain records matching a given condition.

11. Reduce files scanned and accelerate performance with [predictive IO](https://docs.databricks.com/optimizations/predictive-io.html#reduce-files-scanned-and-accelerate-performance-with-predictive-io)

    Predictive IO enables the Photon engine to query less data and serve results even faster. Predictive IO is supported by the serverless and pro types of SQL warehouses, as well as Photon-accelerated clusters running Databricks Runtime 11.2 and above.