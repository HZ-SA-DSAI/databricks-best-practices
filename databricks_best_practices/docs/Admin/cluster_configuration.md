1. [Choose between all purpose clusters and job clusters](https://docs.databricks.com/clusters/cluster-config-best-practices.html#all-purpose-clusters-and-job-clusters)
    1. All-purpose clusters can be shared by multiple users and are best for performing ad-hoc analysis, data exploration, or development. 
    2. Job clusters help reducing resource usage and cost.
2. [Choose appropriate cluster modes](https://docs.databricks.com/clusters/cluster-config-best-practices.html#cluster-mode)
    1. Standard clusters (No Isolation Shared clusters) are recommended for single users only. Standard clusters can run workloads developed in Python, SQL, R, and Scala.
    2. Single Node clusters are intended for jobs that use small amounts of data or non-distributed workloads such as single-node machine learning libraries.
    3. High Concurrency clusters (Shared access mode clusters) are ideal for groups of users who need to share resources or run ad-hoc jobs. Administrators usually create High Concurrency clusters. Databricks recommends enabling autoscaling for High Concurrency clusters.
3. [Consider using spot instances]()
    1. Create clusters using a combination of on-demand and spot instances with a custom spot price.
    2. Launch the cluster so that the Spark driver is on an on-demand instance, which allows saving the state of the cluster even after losing spot instance nodes. 
    3. Turn on "Spot fall back to On-demand"
    4. Use the [Amazon Spot Instance Advisor](https://aws.amazon.com/ec2/spot/instance-advisor/) to determine a suitable price for your instance type and region.