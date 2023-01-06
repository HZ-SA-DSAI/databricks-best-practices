***
# Cluster Configuration

Updated on 01/06/2023

*See below for original doc on docs.databricks.com*

*[Best practices: Cluster configuration](https://docs.databricks.com/clusters/cluster-config-best-practices.html#best-practices-cluster-configuration)*

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

    1. [Data analysis](https://docs.databricks.com/clusters/cluster-config-best-practices.html#data-analysis)
        
        Data analysts typically perform processing requiring data from multiple partitions, leading to many shuffle operations. A cluster with a smaller number of nodes can reduce the network and disk I/O needed to perform these shuffles. 

        Analytical workloads will likely require reading the same data repeatedly, so recommended worker types are storage optimized with Delta Cache enabled.

    2. [Basic batch ETL](https://docs.databricks.com/clusters/cluster-config-best-practices.html#basic-batch-etl)

        Simple batch ETL jobs that don’t require wide transformations, such as joins or aggregations, typically benefit from clusters that are compute-optimized. 

        Compute-optimized worker types are recommended; these will be cheaper, and these workloads will likely not require significant memory or storage.

        Using a pool might provide a benefit for clusters supporting simple ETL jobs by decreasing cluster launch times and reducing total runtime when running job pipelines. However, since these types of workloads typically run as scheduled jobs where the cluster runs only long enough to complete the job, using a pool might not provide a benefit.

    3. [Complex batch ETL](https://docs.databricks.com/clusters/cluster-config-best-practices.html#complex-batch-etl)

        More complex ETL jobs, such as processing that requires unions and joins across multiple tables, will probably work best when you can minimize the amount of data shuffled. Since reducing the number of workers in a cluster will help minimize shuffles, you should consider a smaller cluster over a larger cluster.

        Complex transformations can be compute-intensive, so for some workloads reaching an optimal number of cores may require adding additional nodes to the cluster.

        Like simple ETL jobs, compute-optimized worker types are recommended; these will be cheaper, and these workloads will likely not require significant memory or storage. Also, like simple ETL jobs, the main cluster feature to consider is pools to decrease cluster launch times and reduce total runtime when running job pipelines.

    4. [Training machine learning models](https://docs.databricks.com/clusters/cluster-config-best-practices.html#training-machine-learning-models)

        Since initial iterations of training a machine learning model are often experimental, a smaller cluster is a good choice. A smaller cluster will also reduce the impact of shuffles.

        If stability is a concern, or for more advanced stages, a larger cluster may be a good choice.

        A really large cluster is not recommended due to the overhead of shuffling data between nodes.

        Recommended worker types are storage optimized with Delta Caching enabled to account for repeated reads of the same data and to enable caching of training data. If the compute and storage options provided by storage optimized nodes are not sufficient, consider GPU optimized nodes. A possible downside is the lack of Delta Caching support with these nodes.

13. [Recommendations for Multiple users running data analysis and ad-hoc processing](https://docs.databricks.com/clusters/cluster-config-best-practices.html#multi-user-clusters)

    1. The recommended approach for cluster provisioning is a hybrid approach for node provisioning in the cluster along with autoscaling. A hybrid approach involves defining the number of on-demand instances and spot instances for the cluster and enabling autoscaling between the minimum and the maximum number of instances.
    ![Multiple users](https://docs.databricks.com/_images/scenario-1-1.png)
    2. This cluster is always available and shared by the users belonging to a group by default. Enabling autoscaling allows the cluster to scale up and down depending upon the load.
    3. Users do not have access to start/stop the cluster, but the initial on-demand instances are immediately available to respond to user queries. If the user query requires more capacity, autoscaling automatically provisions more nodes (mostly Spot instances) to accommodate the workload.
    4. Databricks has other features to further improve multi-tenancy use cases:
        1. [Handling large queries in interactive workflows](https://docs.databricks.com/clusters/query-watchdog.html) describes a process to automatically manage queries that will never finish.
        2. [Task preemption](https://docs.databricks.com/clusters/preemption.html) improves how long-running jobs and shorter jobs work together.
        3. [Autoscaling local storage](https://docs.databricks.com/clusters/configure.html#autoscaling-local-storage) helps prevent running out of storage space in a multi-tenant environment.

14. [Recommendations for specialized use cases like machine learning](https://docs.databricks.com/clusters/cluster-config-best-practices.html#specialized-workloads)

    1. The best approach for this kind of workload is to create cluster policies with pre-defined configurations for default, fixed, and settings ranges. These settings might include the number of instances, instance types, spot versus on-demand instances, roles, libraries to be installed, and so forth. 
    2. Using cluster policies allows users with more advanced requirements to quickly spin up clusters that they can configure as needed for their use case and enforce cost and compliance with policies.
    ![Specialized](https://docs.databricks.com/_images/scenario-2.png)
    3. This approach provides more control to users while maintaining the ability to keep cost under control by pre-defining cluster configurations. This also allows you to configure clusters for different groups of users with permissions to access different data sets.
    4. One downside to this approach is that users have to work with administrators for any changes to clusters, such as configuration, installed libraries, and so forth.

15. [Recommendations for scheduled batch jobs](https://docs.databricks.com/clusters/cluster-config-best-practices.html#batch-workloads)
    1. The suggested best practice is to launch a new cluster for each job run. Running each job on a new cluster helps avoid failures and missed SLAs caused by other workloads running on a shared cluster.
    2. Depending on the level of criticality for the job, you could use all on-demand instances to meet SLAs or balance between spot and on-demand instances for cost savings.
    ![Batch](https://docs.databricks.com/_images/scenario-3.png)