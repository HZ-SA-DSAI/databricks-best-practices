***
# Deep Learning

**Updated on 01/04/2023**

*See below for original doc on docs.databricks.com*

*[Best practices for deep learning on Databricks](https://docs.databricks.com/machine-learning/train-model/dl-best-practices.html#best-practices-for-deep-learning-on-databricks)*

1. [Customize the development environment](https://docs.databricks.com/machine-learning/train-model/dl-best-practices.html#customize-the-development-environment)
    1. Use notebook-scoped Python libraries or notebook-scoped R libraries to use a specific set or version of libraries without affecting other cluster users.
    2. Install libraries at the cluster level to standardize versions for a team or a project.
    3. Set up a Databricks job to ensure that a repeated task runs in a consistent, unchanging environment.
2. [Use cluster policies](https://docs.databricks.com/machine-learning/train-model/dl-best-practices.html#use-cluster-policies)
    
    You can create cluster policies to guide data scientists to the right choices, such as using a Single Node cluster for development and using an autoscaling cluster for large jobs.

3. [Loading data](https://docs.databricks.com/machine-learning/train-model/dl-best-practices.html#best-practices-for-loading-data)
    
    Use Delta Lake and Petastorm to optimize data throughput for deep learning applications

4. [Model Training](https://docs.databricks.com/machine-learning/train-model/dl-best-practices.html#best-practices-for-training-deep-learning-models)
    1. Use Machine Learning Runtime, MLflow tracking and autologging
    2. Start with a Single Node cluster
    3. Use TensorBoard and Ganglia to monitor the training process
5. [Performance Tuning](https://docs.databricks.com/machine-learning/train-model/dl-best-practices.html#optimize-performance-for-deep-learning)
    1. Enable early stopping
    2. Tune batch size
    3. Consider transfer learning
6. [Use distributed training options](https://docs.databricks.com/machine-learning/train-model/dl-best-practices.html#move-to-distributed-training)
    1. HorovodRunner
    2. spark-tensorflow-distributor
    3. Hyperopt
7. [Inference](https://docs.databricks.com/machine-learning/train-model/dl-best-practices.html#best-practices-for-inference)
    1. To minimize costs, consider both CPUs and inference-optimized GPUs
    2. Use MLflow to simplify deployment and model serving
    3. If you expect to access data for inference more than once, consider creating a preprocessing job to ETL the data into a Delta Lake table before running the inference job.
    4. Use Spark Pandas UDFs to scale batch and streaming inference across a cluster
    5. Use Databricks Serverless Real-Time Inference and Classic MLflow Model Serving for online inference.