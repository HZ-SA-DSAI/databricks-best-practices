# Clone

*See below for original doc on docs.databricks.com*

*[Clone a table on Databricks](https://docs.databricks.com/optimizations/clone.html#clone-a-table-on-databricks)*

1. You can create a copy of an existing Delta Lake table on Databricks at a specific version using the clone command. Clones can be either deep or shallow.

2. Clone is a Databricks-exclusive feature enabled in the Databricks Runtime by default.

3. Databricks also supports cloning Parquet and Iceberg tables. See [Incrementally clone Parquet and Iceberg tables to Delta Lake.](https://docs.databricks.com/ingestion/clone-parquet.html)

4. A deep clone is a clone that copies the source table data to the clone target in addition to the metadata of the existing table. Additionally, stream metadata is also cloned such that a stream that writes to the Delta table can be stopped on a source table and continued on the target of a clone from where it left off.

5. A shallow clone is a clone that does not copy the data files to the clone target. The table metadata is equivalent to the source. These clones are cheaper to create.

6. Cloud provider permissions:

    1. If you have created a deep clone, any user that reads the deep clone must have read access to the clone’s directory. To make changes to the clone, users must have write access to the clone’s directory.

    2. If you have created a shallow clone, any user that reads the shallow clone needs permission to read the files in the original table, since the data files remain in the source table with shallow clones, as well as the clone’s directory. To make changes to the clone, users will need write access to the clone’s directory.

7. [Use clone for data archiving](https://docs.databricks.com/optimizations/clone.html#use-clone-for-data-archiving)

    Data may need to be kept for longer than is feasible with time travel or for disaster recovery. In these cases, you can create a deep clone to preserve the state of a table at a certain point in time for archival. Incremental archiving is also possible to keep a continually updating state of a source table for disaster recovery.

8. [Use clone for ML model reproduction](https://docs.databricks.com/optimizations/clone.html#use-clone-for-ml-model-reproduction)

    When doing machine learning, you may want to archive a certain version of a table on which you trained an ML model. Future models can be tested using this archived data set.

9. [Use clone for short-term experiments on a production table](https://docs.databricks.com/optimizations/clone.html#use-clone-for-short-term-experiments-on-a-production-table)

    To test a workflow on a production table without corrupting the table, you can easily create a shallow clone. This allows you to run arbitrary workflows on the cloned table that contains all the production data but does not affect any production workloads.

10. [Use clone for data sharing](https://docs.databricks.com/optimizations/clone.html#use-clone-for-data-sharing)

    Other business units within a single organization may want to access the same data but may not require the latest updates. Instead of giving access to the source table directly, you can provide clones with different permissions for different business units. The performance of the clone can exceed that of a simple view.

11. [Use clone to override table properties](https://docs.databricks.com/optimizations/clone.html#use-clone-to-override-table-properties)

    Table property overrides are particularly useful for:

        1. Annotating tables with owner or user information when sharing data with different business units.

        2. Archiving Delta tables and time travel is required. You can specify the log retention period independently for the archive table. For example:

6. Important Notes: 

    6. Any changes made to either deep or shallow clones affect only the clones themselves and not the source table.

    7. Shallow clones reference data files in the source directory. If you run vacuum on the source table clients will no longer be able to read the referenced data files and a FileNotFoundException will be thrown. In this case, running clone with replace over the shallow clone will repair the clone. If this occurs often, consider using a deep clone instead which does not depend on the source table.

    8. Deep clones do not depend on the source from which they were cloned, but are expensive to create because a deep clone copies the data as well as the metadata.

    9. Cloning with replace to a target that already has a table at that path creates a Delta log if one does not exist at that path. You can clean up any existing data by running vacuum.

    10. If an existing Delta table exists, a new commit is created that includes the new metadata and new data from the source table. This new commit is incremental, meaning that only new changes since the last clone are committed to the table.

    11. Cloning a table is not the same as Create Table As Select or CTAS. A clone copies the metadata of the source table in addition to the data. Cloning also has simpler syntax: you don’t need to specify partitioning, format, invariants, nullability and so on as they are taken from the source table.

    12. A cloned table has an independent history from its source table. Time travel queries on a cloned table will not work with the same inputs as they work on its source table.

    13. The metadata that is cloned includes: schema, partitioning information, invariants, nullability. For deep clones only, stream and COPY INTO metadata are also cloned. Metadata not cloned are the table description and user-defined commit metadata.