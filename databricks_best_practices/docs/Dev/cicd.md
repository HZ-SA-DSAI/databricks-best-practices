# CI/CD

*See below for original doc on docs.databricks.com*

*[Use CI/CD](https://docs.databricks.com/dev-tools/index-ci-cd.html#use-cicd)*

*[CI/CD with Jenkins on Databricks](https://docs.databricks.com/dev-tools/ci-cd/ci-cd-jenkins.html)*

1. GitHub Actions
    1. [databricks/run-notebook](https://github.com/databricks/run-notebook/#run-notebook-v0): Executes a Databricks notebook as a one-time Databricks job run, awaits its completion, and returns the notebook’s output.
    2. [databricks/upload-dbfs-temp](https://github.com/databricks/upload-dbfs-temp/#upload-dbfs-temp-v0): Uploads a file to a temporary DBFS path for the duration of the current GitHub Workflow job. Returns the path of the DBFS tempfile.
2. Typical Databricks CI/CD pipeline:
    1. Continuous integration
        1. Code
            1. Develop code and unit tests in a Databricks notebook or using an external IDE.
            2. Manually run tests.
            3. Commit code and tests to a git branch.
        2. Build
            1. Gather new and updated code and tests.
            2. Run automated tests.
            3. Build libraries and non-notebook Apache Spark code.
        3. Release: Generate a release artifact.
    2. Continuous delivery:
        1. Deploy
            1. Deploy notebooks.
            2. Deploy libraries.
        2. Test: Run automated tests and report results.
        3. Operate: Programmatically schedule data engineering, analytics, and machine learning workflows.
3. [See here for a walkthrough of CI/CD with Jenkins on Databricks](https://docs.databricks.com/dev-tools/ci-cd/ci-cd-jenkins.html)
4. [See here for the option to Orchestrate Databricks jobs with Apache Airflow](https://docs.databricks.com/workflows/jobs/how-to-use-airflow-with-jobs.html)