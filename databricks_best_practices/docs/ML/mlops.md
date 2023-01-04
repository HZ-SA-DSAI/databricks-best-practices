***
# MLOps

**Updated on 01/04/2023**

*See below for original doc on docs.databricks.com*

*[MLOps workflow on Databricks](https://docs.databricks.com/machine-learning/mlops/mlops-workflow.html#mlops-workflow-on-databricks)*

1. [Create a separate environment for each stage in Dev, Stage, and Prod](https://docs.databricks.com/machine-learning/mlops/mlops-workflow.html#create-a-separate-environment-for-each-stage)
2. [Use Git for version control](https://docs.databricks.com/machine-learning/mlops/mlops-workflow.html#access-control-and-versioning)
    
    Use [Databricks Repos](https://docs.databricks.com/repos/index.html) to integrate with your Git provider and sync notebooks and source code with Databricks workspaces.

3. [Store data in a Lakehouse architecture using Delta tables](https://docs.databricks.com/lakehouse/index.html)
4. [Manage models and model development with MLflow](https://docs.databricks.com/mlflow/index.html)
5. [Deploy code, not models](https://docs.databricks.com/machine-learning/mlops/deployment-patterns.html)

    In most situations, Databricks recommends that during the ML development process, you promote code, rather than models, from one environment to the next.

6. [Recommended MLOps workflow](https://docs.databricks.com/machine-learning/mlops/mlops-workflow.html#recommended-mlops-workflow)
    1. [Development](https://docs.databricks.com/machine-learning/mlops/mlops-workflow.html#development-stage-1)
    ![Development](https://docs.databricks.com/_images/mlops-dev-diagram.png)
    2. [Staging](https://docs.databricks.com/machine-learning/mlops/mlops-workflow.html#staging-stage-1)
    ![Staging](https://docs.databricks.com/_images/mlops-staging-diagram.png)
    3. [Production](https://docs.databricks.com/machine-learning/mlops/mlops-workflow.html#production-stage-1)
    ![Production](https://docs.databricks.com/_images/mlops-prod-diagram.png)