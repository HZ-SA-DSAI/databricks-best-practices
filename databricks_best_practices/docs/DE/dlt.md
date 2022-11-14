1. [Make expectations portable and reusable](https://docs.databricks.com/workflows/delta-live-tables/delta-live-tables-cookbook.html#make-expectations-portable-and-reusable)
    1. Maintain data quality rules separately from your pipeline implementations.
    2. Store the rules in a format that is reliable and easy to access and update, for example, a text file stored in DBFS or cloud storage or a Delta table.
2. [Use Python UDFs in SQL](https://docs.databricks.com/workflows/delta-live-tables/delta-live-tables-cookbook.html#use-python-udfs-in-sql)
    
    You want the simplicity of SQL to define Delta Live Tables datasets but need transformations not directly supported in SQL.

3. [Use MLFlow models in a Delta Live Tables pipeline](https://docs.databricks.com/workflows/delta-live-tables/delta-live-tables-cookbook.html#use-mlflow-models-in-a-delta-live-tables-pipeline)
    1. Obtain the run ID and model name of the MLFlow model. 
    2. Use the URI to define a Spark UDF to load the MLFlow model.
    3. Call the UDF in your table definitions to use the MLFlow model.
4. [Create sample datasets for development and testing](https://docs.databricks.com/workflows/delta-live-tables/delta-live-tables-cookbook.html#create-sample-datasets-for-development-and-testing)
    1. Implement your transformation logic in a single or shared set of notebooks.
    2. Then create separate notebooks to define multiple datasets based on environment.
    3. Then create notebooks that define a sample of data based on requirements. 
    4. To use these different datasets, create multiple pipelines with the notebooks implementing the transformation logic. 
5. [Programmatically manage and create multiple live tables](https://docs.databricks.com/workflows/delta-live-tables/delta-live-tables-cookbook.html#programmatically-manage-and-create-multiple-live-tables)
    1. You can use a metaprogramming pattern to reduce the overhead of generating and maintaining redundant flow definitions. 
    2. Metaprogramming in Delta Live Tables is done using Python inner functions.
    3. Because these functions are lazily evaluated, you can use them to create flows that are identical except for input parameters.
    4. Each invocation can include a different set of parameters that controls how each table should be generated
6. [Quarantine invalid data](https://docs.databricks.com/workflows/delta-live-tables/delta-live-tables-cookbook.html#quarantine-invalid-data)
    
    Create rules that are the inverse of the expectations you’ve defined and use those rules to save the invalid records to a separate table. You can programmatically create these inverse rules. 

    A disadvantage of the above approach is that it generates the quarantine table by processing the data twice. If you don’t want this performance overhead, you can use the constraints directly within a query to generate a column indicating the validation status of a record. You can then partition the table by this column for further optimization.

7. [Validate row counts across tables](https://docs.databricks.com/workflows/delta-live-tables/delta-live-tables-cookbook.html#validate-row-counts-across-tables)
    1. You need to compare row counts between two live tables, perhaps to verify that data was processed successfully without dropping rows.
    2. Add an additional table to your pipeline that defines an expectation to perform the comparison. 
8. [Use secrets in a pipeline](https://docs.databricks.com/workflows/delta-live-tables/delta-live-tables-cookbook.html#use-secrets-in-a-pipeline)
    
    Use Databricks [secrets](https://docs.databricks.com/security/secrets/index.html) to store credentials such as access keys or passwords. To configure the secret in your pipeline, use a Spark property in the [pipeline settings](https://docs.databricks.com/workflows/delta-live-tables/delta-live-tables-configuration.html) cluster configuration.

9. [Define limits on pipeline clusters](https://docs.databricks.com/workflows/delta-live-tables/delta-live-tables-cookbook.html#define-limits-on-pipeline-clusters)

    [Cluster policies](https://docs.databricks.com/administration-guide/clusters/policies.html) allow you to define templates that limit user access to cluster configuration. You can define one or more cluster policies to use when configuring pipelines.

    To create a cluster policy for Delta Live Tables pipelines, define a cluster policy with the cluster_type field set to dlt.

10. [Perform advanced validation with Delta Live Tables expectations](https://docs.databricks.com/workflows/delta-live-tables/delta-live-tables-cookbook.html#perform-advanced-validation-with-delta-live-tables-expectations)

    You can define live tables using aggregate and join queries and use the results of those queries as part of your expectation checking. 
    
    To prevent persistence of the table used in the validation, use the TEMPORARY keyword in the table definition.
