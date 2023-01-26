***
# Migrate EDW to Databricks

**Updated on 01/25/2023**

*See below for original doc on docs.databricks.com*

*[Migrate your enterprise data warehouse to the Databricks Lakehouse](https://docs.databricks.com/migration/warehouse-to-lakehouse.html#migrate-your-enterprise-data-warehouse-to-the-databricks-lakehouse)*

1. The lakehouse allows you to leverage the same data stored in the data lake in queries and systems that usually rely on a data warehouse.

2. Migrating from a data warehouse to the lakehouse generally involves reducing the complexity of your data architecture and workflows.

3. Most workloads, queries, and dashboards defined in data warehouses can run with minimal code refactoring once admins have completed initial data migration and governance configuration. 

4. Load data into the lakehouse

    1. [Migrate Parquet Data Lake](https://docs.databricks.com/migration/parquet-to-delta-lake.html)
        1. [Deep CLONE Parquet](https://docs.databricks.com/migration/parquet-to-delta-lake.html#migrate-parquet-data-with-clone-parquet)
        2. [Shallow CLONE Parquet](https://docs.databricks.com/migration/parquet-to-delta-lake.html#migrate-parquet-data-with-clone-parquet)
        3. [CONVERT TO DELTA](https://docs.databricks.com/migration/parquet-to-delta-lake.html#migrate-parquet-data-with-convert-to-delta)
        4. [Auto Loader](https://docs.databricks.com/migration/parquet-to-delta-lake.html#migrate-parquet-data-with-auto-loader)
        5. [Batch Spark job](https://docs.databricks.com/migration/parquet-to-delta-lake.html#migrate-parquet-data-with-custom-apache-spark-batch-logic)
    2. [Use query federation](https://docs.databricks.com/query-federation/index.html)

        The term query federation describes a collection of features that enable users and systems to run queries against multiple siloed data sources without needing to migrate all data to a unified system.

        Databricks SQL allows you to configure read-only connections to popular database solutions with drivers included on all serverless and pro SQL warehouses.

    3. [Use Databricks Partner Connect](https://docs.databricks.com/partner-connect/index.html)

        Partner Connect lets you create trial accounts with select Databricks technology partners and connect your Databricks workspace to partner solutions from the Databricks UI. 

    4. [Multiple ways to load data into Databricks Lakehouse](https://docs.databricks.com/ingestion/index.html#load-data-into-the-databricks-lakehouse)

        1. [Auto Loader](https://docs.databricks.com/ingestion/index.html#auto-loader)
        2. [Delta Live Tables and Auto Loader](https://docs.databricks.com/ingestion/index.html#automate-etl-with-delta-live-tables-and-auto-loader)
        3. [COPY INTO](https://docs.databricks.com/ingestion/index.html#copy-into)
        4. [Convert to Delta](https://docs.databricks.com/ingestion/index.html#convert-to-delta)
        5. [Use Apache Spark to load data from external sources](https://docs.databricks.com/ingestion/index.html#use-apache-spark-to-load-data-from-external-sources)

    5. [Use Delta Live Tables](https://docs.databricks.com/workflows/delta-live-tables/index.html)

        Delta Live Tables is a framework for building reliable, maintainable, and testable data processing pipelines.

5. [Differences between Databricks and traditional EDW](https://docs.databricks.com/migration/warehouse-to-lakehouse.html#how-is-the-databricks-lakehouse-different-than-a-data-warehouse)

    1. All transactions are table-level. There are no database-level transactions, locks, or guarantees.
    2. There are no BEGIN and END constructs, meaning each statement or query runs as a separate transaction.
    3. Three tier namespacing uses catalog.schema.table pattern. The terms database and schema are synonymous due to legacy Apache Spark syntax.
    4. Primary key and foreign key constraints are informational only. Constraints can only be enforced at a table level. [See Constraints on Databricks.](https://docs.databricks.com/tables/constraints.html)
    5. Native data types supported in Databricks and Delta Lake might differ slightly from source systems. Required precision for numeric types should be clearly indicated before target types are chosen.