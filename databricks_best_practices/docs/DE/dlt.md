# Delta Live Tables best practices

1. To ensure your pipelines are efficient and maintainable, choose the best dataset type, either a table or a view, when you implement your pipeline queries.

2. Consider using a view when:

    1. You have a large or complex query that you want to break into easier-to-manage queries.
    2. You want to validate intermediate results using expectations.
    3. You want to reduce storage and compute costs and do not require the materialization of query results. Because tables are materialized, they require additional computation and storage resources.

3. Consider using a table when:

    1. Multiple downstream queries consume the table. Because views are computed on demand, the view is re-computed every time the view is queried.
    2. The table is consumed by other pipelines, jobs, or queries. Because views are not materialized, you can only use them in the same pipeline.
    3. You want to view the results of a query during development. Because tables are materialized and can be viewed and queried outside of the pipeline, using tables during development can help validate the correctness of computations. After validating, convert queries that do not require materialization into views.

4. Do not override the Spark version in your pipeline configuration

    Because Delta Live Tables clusters run on a custom version of Databricks Runtime, you cannot manually set the Spark version in cluster configurations. Manually setting a version may result in pipeline failures.

5. Use larger pipelines to:

    1. More efficiently use cluster resources.
    2. Reduce the number of pipelines in your workspace.
    3. Reduce the complexity of workflow orchestration.

6. Use more than one pipeline to:

    1. Split functionality at team boundaries. For example, your data team may maintain pipelines to transform data while your data analysts maintain pipelines that analyze the transformed data.
    2. Split functionality at application-specific boundaries to reduce coupling and facilitate the re-use of common functionality.

7. Use autoscaling to increase efficiency and reduce resource usage

    Use [Enhanced Autoscaling](https://docs.databricks.com/workflows/delta-live-tables/delta-live-tables-concepts.html#auto-scaling) to optimize the cluster utilization of your pipelines. Enhanced Autoscaling adds additional resources only if the system determines those resources will increase pipeline processing speed. Resources are freed as soon as they are no longer needed, and clusters are shut down as soon as all pipeline updates complete.

    Leave the Min workers setting at the default.

    Set the Max workers setting to a value based on budget and pipeline priority.

    Leave instance types unset to allow the Delta Live Tables runtime to pick the best instance types for your workload.

