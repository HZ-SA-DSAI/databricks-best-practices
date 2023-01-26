***
# Migrate ML workloads to Databricks

**Updated on 01/25/2023**

*See below for original doc on docs.databricks.com*

*[Unify your ML, data science, and analytics workloads](https://docs.databricks.com/migration/index.html#unify-your-ml-data-science-and-analytics-workloads)*

1. Pandas workloads in Jupyter notebooks can be synced and run using [Databricks Repos. ](https://docs.databricks.com/repos/index.html)

2. Databricks provides [native support for pandas](https://docs.databricks.com/pandas/index.html) in all Databricks Runtime versions.

    1. In Databricks Runtime 10.0 and above, [Pandas API on Spark](https://docs.databricks.com/pandas/pandas-on-spark.html) provides familiar pandas commands on top of PySpark DataFrames. 
    2. You can also [convert DataFrames between pandas and PySpark](https://docs.databricks.com/pandas/pyspark-pandas-conversion.html).
    3. Apache Spark includes Arrow-optimized execution of Python logic in the form of [pandas function APIs](https://docs.databricks.com/pandas/pandas-function-apis.html), which allow users to apply pandas transformations directly to PySpark DataFrames. 
    4. Apache Spark also supports [pandas UDFs](https://docs.databricks.com/udf/pandas.html), which use similar Arrow-optimizations for arbitrary user functions defined in Python.

3. Databricks configures many popular ML and deep learning libraries in the [Databricks ML Runtime](https://docs.databricks.com/runtime/mlruntime.html)

4. If you sync your local workloads using Git and [Files in Repos](https://docs.databricks.com/files/workspace.html), you can use the same relative paths for data and custom libaries present in your local environment.

5. By default, Databricks maintains .ipynb extensions for Jupyter notebooks synced with Databricks Repos, but automatically converts Jupyter notebooks to Databricks notebooks when [imported with the UI](https://docs.databricks.com/notebooks/notebook-export-import.html#import-notebook). 

6. Databricks notebooks save with a .py extension, and so can live side-by-side with Jupyter notebooks in a Git repository.