# SQL Warehouses

*See below for original doc on docs.databricks.com*

*[What is a Databricks SQL warehouse?](https://docs.databricks.com/sql/admin/sql-endpoints.html#what-is-a-databricks-sql-warehouse)*

1. [Use SQL Serverless to simplify SQL warehouse management and accelerate launch times](https://docs.databricks.com/serverless-compute/index.html)
2. [Understand the feature differences among serverless, pro, and classic SQL warehouses](https://docs.databricks.com/sql/admin/warehouse-type.html#comparing-features-of-serverless-pro-and-classic-sql-warehouses)
    1. Pro allows Predictive I/O, Query federation, Workflows integration, and Geospatial functions compared with Classic
    2. Serverless includes all features of Pro, and provide instant, elastic, fully-managed compute
3. Use preview channel to test your queries and dashboards against upcoming changes. Do not use preview channel for production workloads. 
4. [Use Auto Stop to save costs during idle time](https://docs.databricks.com/sql/admin/sql-endpoints.html#create-a-sql-warehouse)
    1. Pro and classic SQL warehouses: The default is 45 minutes, which is recommended for typical use. The minimum is 10 minutes.
    2. Serverless SQL warehouses: The default is 10 minutes, which is recommended for typical use. The minimum is 5 minutes when you use the UI. Note that you can create a serverless SQL warehouse using the SQL warehouses API, in which case you can set the Auto Stop value as low as 1 minute.
5. To handle more concurrent users for a given query, increase the cluster count.
6. Add tags to help monitor SQL warehouse usage.
7. Configure the spot instance policy for the SQL warehouse (pro and classic SQL warehouses only).

    The spot instance policy determines whether workers use only on-demand instances or a combination of on-demand and spot instances. Cost Optimized uses mostly spot instances and one on-demand instance. Reliability Optimized uses only on-demand instances.

