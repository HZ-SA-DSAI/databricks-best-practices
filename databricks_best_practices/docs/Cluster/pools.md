***
# Pools

Updated on 01/06/2023

*See below for original doc on docs.databricks.com*

*[Best practices: pools](https://docs.databricks.com/clusters/instance-pools/pool-best-practices.html#best-practices-pools)*

1. Databricks [pools](https://docs.databricks.com/clusters/instance-pools/index.html) reduce cluster start and scale-up times by maintaining a set of available, ready-to-use instances.

2. How do pools work?

    ![Pools](https://docs.databricks.com/_images/instance-allocation-from-pool.png)

3. [Create pools based on workloads](https://docs.databricks.com/clusters/instance-pools/pool-best-practices.html#create-pools-based-on-workloads)

    1. If your driver node and worker nodes have different requirements, create a different pool for each.

    2. You can minimize instance acquisition time by creating a pool for each instance type and Databricks runtime your organization commonly uses. 
    
        For example, if most data engineering clusters use instance type A, data science clusters use instance type B, and analytics clusters use instance type C, create a pool with each instance type.

    3. Configure pools to use on-demand instances for jobs with short execution times and strict execution time requirements. Use on-demand instances to prevent acquired instances from being lost to a higher bidder on the spot market.

    4. Configure pools to use spot instances for clusters that support interactive development or jobs that prioritize cost savings over reliability.

4. [Tag pools to manage cost and billing](https://docs.databricks.com/clusters/instance-pools/pool-best-practices.html#tag-pools-to-manage-cost-and-billing)

    1. Tagging pools to the correct cost center allows you to manage cost and usage chargeback. You can use multiple custom tags to associate multiple cost centers to a pool.

    2. Tags from the pools propagate to the underlying cloud provider instances, but the cluster’s tags do not. Apply all custom tags required for managing chargeback of the cloud provider compute cost to the pool.

    3. Pool tags and cluster tags both propagate to Databricks billing. You can use the combination of cluster and pool tags to manage chargeback of Databricks Units.

    ![Databricks Object Tagging Hierarchy](https://docs.databricks.com/_images/object-tagging-hierarchy.png)

5. [Configure pools to control cost](https://docs.databricks.com/clusters/instance-pools/pool-best-practices.html#configure-pools-to-control-cost)

    1. Set the [Min Idle](https://docs.databricks.com/clusters/instance-pools/configure.html#minimum-idle-instances) instances to 0 to avoid paying for running instances that aren’t doing work. The tradeoff is a possible increase in time when a cluster needs to acquire a new instance.

    2. Set the [Idle Instance Auto Termination](https://docs.databricks.com/clusters/instance-pools/configure.html#idle-instance-auto-termination) time to provide a buffer between when the instance is released from the cluster and when it’s dropped from the pool. Set this to a period that allows you to minimize cost while ensuring the availability of instances for scheduled jobs.

    3. Set the [Max Capacity](https://docs.databricks.com/clusters/instance-pools/configure.html#maximum-capacity) based on anticipated usage. Databricks recommends that you set the maximum capacity only if there is a strict instance quota or budget constraint.

4. [Pre-populate pools](https://docs.databricks.com/clusters/instance-pools/pool-best-practices.html#pre-populate-instance-pools)

    1. To benefit fully from pools, you can pre-populate newly created pools. Set the Min Idle instances greater than zero in the pool configuration. 
    2. Alternatively, if you’re following the recommendation to set this value to zero, use a starter job to ensure that newly created pools have available instances for clusters to access.
    3. With the starter job approach, schedule a job with flexible execution time requirements to run before jobs with more strict performance requirements or before users start using interactive clusters. After the job finishes, the instances used for the job are released back to the pool. Set Min Idle instance setting to 0 and set the Idle Instance Auto Termination time high enough to ensure that idle instances remain available for subsequent jobs.
    4. Using a starter job allows the pool instances to spin up, populate the pool, and remain available for downstream job or interactive clusters.