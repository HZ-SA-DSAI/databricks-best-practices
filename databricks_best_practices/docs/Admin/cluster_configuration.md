# Cluster Configuration

1. [Choose between all purpose clusters and job clusters](https://docs.databricks.com/clusters/cluster-config-best-practices.html#all-purpose-clusters-and-job-clusters)
    1. All-purpose clusters can be shared by multiple users and are best for performing ad-hoc analysis, data exploration, or development. 
    2. Job clusters help reducing resource usage and cost.
2. [Choose appropriate cluster modes](https://docs.databricks.com/clusters/cluster-config-best-practices.html#cluster-mode)
    1. Standard clusters (No Isolation Shared clusters) are recommended for single users only. Standard clusters can run workloads developed in Python, SQL, R, and Scala.
    2. Single Node clusters are intended for jobs that use small amounts of data or non-distributed workloads such as single-node machine learning libraries.
    3. High Concurrency clusters (Shared access mode clusters) are ideal for groups of users who need to share resources or run ad-hoc jobs. Administrators usually create High Concurrency clusters. Databricks recommends enabling autoscaling for High Concurrency clusters.
3. [Consider using spot instances](https://docs.databricks.com/clusters/cluster-config-best-practices.html#on-demand-and-spot-instances)
    1. Create clusters using a combination of on-demand and spot instances with a custom spot price.
    2. Launch the cluster so that the Spark driver is on an on-demand instance, which allows saving the state of the cluster even after losing spot instance nodes. 
    3. Turn on "Spot fall back to On-demand"
    4. Use the [Amazon Spot Instance Advisor](https://aws.amazon.com/ec2/spot/instance-advisor/) to determine a suitable price for your instance type and region.
4. [Turn on Autoscaling](https://docs.databricks.com/clusters/cluster-config-best-practices.html#autoscaling)
    1. Balance cost and performance by tuning number of minimum workers.
    2. Some workloads are not compatible with autoscaling clusters, including spark-submit jobs and some Python packages.
    3. If Delta Caching is being used, any cached data on a node is lost if that node is terminated.
    4. Turn on autoscaling for local storage. With autoscaling local storage, Databricks monitors the amount of free disk space available on your cluster’s Spark workers. 
5. [Use cluster pools to improve processing time while minimizing cost](https://docs.databricks.com/clusters/instance-pools/index.html)
6. Use the latest Databricks Runtime version for all-purpose clusters.
7. For job clusters running operational workloads, consider using the Long Term Support (LTS) Databricks Runtime version. 
8. [Use cluster policies to enforce controls over the creation and configuration of clusters](https://docs.databricks.com/administration-guide/clusters/policies-best-practices.html#best-practices-cluster-policies)
9. Tune auto termination period length
10. [Configure instances with smaller RAM sizes, and deploy more instances if you need more memory for your jobs](https://docs.databricks.com/clusters/cluster-config-best-practices.html#garbage-collection)
    
    To minimize the impact of long garbage collection sweeps, avoid deploying clusters with large amounts of RAM configured for each instance. Having more RAM allocated to the executor will lead to longer garbage collection times. 

11. [Use cluster tags to monitor the cost of cloud resources used by different groups in your organization](https://docs.databricks.com/clusters/configure.html#cluster-tags)
12. [Determine appropriate cluster sizes based on use cases including Data Analysis, Batch ETL, ML, etc.](https://docs.databricks.com/clusters/cluster-config-best-practices.html#cluster-sizing-considerations)