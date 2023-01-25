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
    3. [Low shuffle merge](https://docs.databricks.com/optimizations/low-shuffle-merge.html) reduces the number of data files rewritten by MERGE operations and reduces the need to recaculate ZORDER clusters.
    4. Apache Spark 3.0 introduced [adaptive query execution](https://docs.databricks.com/optimizations/aqe.html), which provides enhanced performance for many operations.

2. You can [clone](https://docs.databricks.com/optimizations/clone.html) tables on Databricks to make deep or shallow copies of source datasets.

3. The [cost-based optimizer](https://docs.databricks.com/optimizations/cbo.html) accelerates query performance by leveraging table statistics.

4. You can [auto optimize](https://docs.databricks.com/optimizations/auto-optimize.html) Delta tables using optimized writes and automatic file compaction; this is especially useful for long-running Structured Streaming jobs.

5. You can use Spark SQL to interact with [semi-structured JSON](https://docs.databricks.com/optimizations/semi-structured.html) data without parsing strings.

6. [Higher order functions](https://docs.databricks.com/optimizations/higher-order-lambda-functions.html) provide built-in, optimized performance for many operations that do not have common Spark operators. Higher order functions provide a performance benefit over user defined functions.

7. Databricks provides a number of built-in operators and special syntax for working with [complex data types](https://docs.databricks.com/optimizations/complex-types.html), including arrays, structs, and JSON strings.

8. You can manually tune settings for joins that include [ranges](https://docs.databricks.com/optimizations/range-join.html) or contain data with substanial [skew](https://docs.databricks.com/optimizations/skew-join.html).

9. Databricks provides a write serializable isolation guarantee by default; changing the [isolation level](https://docs.databricks.com/optimizations/isolation-level.html) to serializable can reduce throughput for concurrent operations, but might be necessary when read serializability is required.

10. You can use [bloom filter indexes](https://docs.databricks.com/optimizations/bloom-filters.html) to reduce the likelihood of scanning data files that don’t contain records matching a given condition.