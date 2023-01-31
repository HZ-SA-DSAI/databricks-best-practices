***
# Cost Management

Updated on 01/31/2023

*See below for original doc on https://www.databricks.com/blog/*

*[Best Practices for Cost Management on Databricks](https://www.databricks.com/blog/2022/10/18/best-practices-cost-management-databricks.html)*

1. Understand what is a dbu
    1. A Databricks Unit (DBU) is the underlying unit of consumption within the platform. 
    
        1. With the exception of a SQL Warehouse, the amount of DBUs consumed is based on the number of nodes and the computational power of the underlying VM instance types that are part of the respective cluster (as SQL warehouses are essentially a group of clusters, the DBU rate is the sum DBU rates of the clusters making up the endpoint). 
        2. At the highest level, each cloud will have slightly different DBU rates for similar clusters (since node types vary across clouds), but the Databricks website has DBU calculators for each supported cloud provider ([AWS](https://www.databricks.com/product/aws-pricing/instance-types) | [Azure](https://azure.microsoft.com/en-us/pricing/calculator/) | [GCP](https://www.databricks.com/product/gcp-pricing/instance-types)).

    2. To convert DBU usage to dollar amounts, you'll need the DBU rate of the cluster, as well as the workload type that generated the respective DBU (ex. Automated Job, All-Purpose Compute, Delta Live Tables, SQL Compute, Serverless Compute) and the subscription plan tier (Standard and Premium for Azure and GCP; Standard, Premium, and Enterprise for AWS). 
    
        For example, an Enterprise Databricks workspace has a Jobs DBU list rate at 20 cents/DBU on AWS. With an instance type which runs at 3 DBU/hour, a 4 node jobs cluster would be charged at $2.40 ($0.2 * 3 * 4) for an hour. [DBU calculators](https://www.databricks.com/product/aws-pricing/instance-types) can be used to calculate total charges and list prices are summarized in a cloud-specific matrix including SKU and tier ([AWS](https://www.databricks.com/product/aws-pricing) | [Azure](https://azure.microsoft.com/en-us/pricing/details/databricks/) | [GCP](https://www.databricks.com/product/gcp-pricing)).

2. Manage costs through cluster policies

    A [cluster policy](https://docs.databricks.com/administration-guide/clusters/policies.html) allows an admin to control the set of configurations that are available when creating a new cluster and these policies can be assigned to individual users or user groups.

    Though at an initial glance it may seem that more restrictive clusters lead to lower costs, this is not always the case. Very restrictive policies lead to clusters which cannot finish tasks in a timely manner leading to higher costs from long running jobs. Therefore it's imperative to take a use-case driven approach when formulating cluster policies giving teams the right amount of compute power for their workloads.

3. Enforce auto-scaling

    Auto-scaling is suitable for:

    1. Shared all-purpose compute clusters: a team can share one cluster for ad-hoc analysis and experimental jobs or machine learning workloads.
    2. Longer-running batch jobs with varying complexity: jobs can leverage auto-scaling so that the cluster scales to the degree of resources needed.

    Additional Note:

    1. Note that jobs using auto-scaling should not be time sensitive as scaling the cluster up can delay completion due to node startup time. To help alleviate this, use an [instance pool](https://docs.databricks.com/clusters/instance-pools/index.html) whenever possible.
    2. Standard streaming workloads have not historically been able to benefit from autoscaling; they would simply scale to the maximum node count and stay there for the duration of the job. A more production-level ready option for teams working on these types of workloads is to leverage [Delta Live Tables](https://www.databricks.com/product/delta-live-tables) and [enhanced auto-scaling](https://docs.databricks.com/data-engineering/delta-live-tables/delta-live-tables-concepts.html#databricks-enhanced-autoscaling)
    3. Despite DLT being developed with streaming workloads in mind, it is just as applicable for batch pipelines by leveraging the [Trigger.AvailableNow](https://docs.databricks.com/ingestion/auto-loader/production.html#using-triggeravailablenow-and-rate-limiting) option allowing for incremental updates of target tables.

4. Use single node clusters for:

    New users looking to explore the platform, data science teams who are leveraging non-distributed ML libraries, as well as any users needing to do lightweight exploratory data analysis.

5. Enforce Auto-termination

6. Use latest Databricks runtimes

    [Databricks Runtimes](https://docs.databricks.com/runtime/index.html) are an important part of performance optimization on Databricks; customers often see an automatic benefit in switching to a cluster running a newer runtime without many other changes to their configuration. 

7. Use Photon

    [Photon](https://www.databricks.com/blog/2022/08/03/announcing-photon-engine-general-availability-on-the-databricks-lakehouse-platform.html) will intelligently accelerate parts of a workload through a vectorized Spark engine with which customers see a 3x to 8x increase in performance. The massive increase in performance leads to quicker jobs and consequently lower total costs.

8. Use spot instances

    When running fault tolerant processes such as experimental workloads or ad-hoc queries where reliability and duration of the workload aren't a priority, spot instances can provide an easy way to keep instance costs down. Hence, spot instances are best suited for development and staging environments.

    Note that Azure has an additional lever in cost control: [reserved instances](https://docs.microsoft.com/en-us/azure/cost-management-billing/reservations/prepay-databricks-reserved-capacity) can be used by Databricks, providing another (potentially steep) discount without adding instability.

9. On AWS, consider using AWS Graviton enabled VMs which are built on Arm64 instruction set architecture

    Based on studies provided by AWS in addition to [benchmarks run with Databricks using Photon](https://www.databricks.com/blog/2022/04/18/announcing-databricks-support-for-aws-graviton2-with-up-to-3x-better-price-performance.html), these Graviton enabled instances have some of the best price to performance ratios available in the AWS EC2 instance type set.

10. Enforce cluster tagging

    1. These tags propagate down to the cloud provider level so that usage and costs can be attributed both from the Databricks platform as well as the underlying cloud costs. 

    2. Once a tag to identify the team using the cluster is assigned, administrators can analyze the [usage logs](https://docs.databricks.com/administration-guide/account-settings-e2/usage.html#usage-download) to tie back DBUs and costs generated back to the team leveraging the cluster. These tags will also propagate down to the VM usage level so that cloud provider instance costs can also be attributed back to the team or cost center. 

    3. An important distinction regarding cluster tags when using a cluster pool is that only the cluster pool tags (and not the cluster tags) [propagate down to underlying VM instances](https://docs.databricks.com/administration-guide/account-settings/usage-detail-tags-aws.html#tag-propagation). 
    
        1. Cluster pool creation is not restricted by cluster policies and hence an administrator should create cluster pools with the appropriate tags prior to assigning usage permissions to a team. 
        2. The team can then have access through policies to attach to the respective pool when creating their clusters. 
        3. This ensures that tags associated with the team using the pool are propagated down to the VM instance level for billing.

11. Policy virtual attributes

    Outside of the settings that are seen on the cluster configuration page, there are also "virtual" attributes that can be restricted by policies. Specifically the two attributes available in this category are "dbus_per_hour" and "cluster_type".

    With the "dbus_per_hour" attribute, creators of clusters can have some flexibility in configuration as long as the DBU usage falls below the set restriction provided in the policy. 

    The other virtual attribute available is "cluster_type" which can be leveraged to restrict users from the different types of clusters. The types which are allowable through this attribute are "all-purpose", "job", and "dlt" with the last one referring to Delta Live Tables. 

12. Storage considerations

    If your storage lifecycle ages objects out before they can be vacuumed by Delta, your tables may break; be sure to test any lifecycle policies on non-production data before implementing them more widely. 

13. Networking considerations

    1. To reduce network costs, Databricks workspaces should be deployed with the goal of minimizing the amount of data being transferred between regions and availability zones. This includes deploying in the same region as the majority of your data where possible, and might include launching regional workspaces if necessary.

    2. When using a customer-managed VPC for a Databricks workspace on AWS, networking costs can be reduced by leveraging [VPC Endpoints](https://docs.databricks.com/administration-guide/cloud-configurations/aws/customer-managed-vpc.html#configure-regional-endpoints-optional) which allow connectivity between the VPC and AWS services without an Internet Gateway or NAT Device. 
        1. Using endpoints reduces the costs incurred from network traffic and also makes the connection more secure. 
        2. Gateway endpoints specifically can be used for connecting to S3 and DynamoDB while interface endpoints can similarly be used to reduce the cost of compute instances connecting to the Databricks control plane. 
        3. These endpoints are available as long as the workspace uses [Secure Cluster Connectivity](https://docs.databricks.com/security/secure-cluster-connectivity.html).

    3. Similarly on Azure, [Private Link or Service Endpoints](https://www.databricks.com/blog/2020/02/28/securely-accessing-azure-data-sources-from-azure-databricks.html) can be configured for Databricks to communicate with services such as ADLS to reduce NAT costs. 
    4. On GCP, [Private Google Access (PGA)](https://docs.gcp.databricks.com/administration-guide/cloud-configurations/gcp/firewall.html#firewall-configuration-overview) can be leveraged so that the traffic between Google Cloud Storage (GCS) and Google Container Registry (GCR) uses Google's internal network rather than the public internet, consequently also bypassing the use of a NAT device.

14. Monitoring Usage

    1. With the Databricks Enterprise 2.0 architecture the account console includes a [usage page](https://docs.databricks.com/administration-guide/account-settings-e2/usage.html#access-the-usage-page-1) providing admins the ability to see usage by DBU or Dollar amount visually. 
    2. Admins have the option to download usage logs manually from the account console [usage page](https://docs.databricks.com/administration-guide/account-settings-e2/usage.html#usage-download-1) or with the [Account API](https://docs.databricks.com/administration-guide/account-settings/billable-usage-download-api.html). However, a more efficient process for analyzing these usage logs is to configure automated log delivery to cloud storage ([AWS](https://docs.databricks.com/administration-guide/account-settings/billable-usage-delivery.html), [GCP](https://docs.gcp.databricks.com/administration-guide/account-settings-gcp/log-delivery.html)). This results in a daily CSV which contains the usage for each workspace in a [granular schema](https://docs.databricks.com/administration-guide/account-settings/usage-analysis.html#csv-file-schema).
    3. Once usage log delivery has been configured in any of the three clouds, a common best practice is to create a data pipeline within Databricks that will ingest this data daily and save it into a Delta table using a scheduled workflow. This data can then be used for [usage analysis](https://docs.databricks.com/administration-guide/account-settings/usage-analysis.html#usage-analysis-dashboard-notebook) or to trigger [alerts](https://docs.databricks.com/sql/user/alerts/index.html) notifying admins or team leaders accountable for cost center spend when consumption reaches a set threshold.

15. Budgets API

    One upcoming feature to make budgeting on Databricks compute costs easier is the new [budget endpoint](https://docs.databricks.com/dev-tools/api/latest/account.html#operation/create-budget) (currently in Private Preview) within the Account API. This will allow for anyone using a Databricks workspace to get notified once a budget threshold is hit on any custom timeframe filtered by workspace, SKU, or cluster tag. Hence, a budget can be configured for any workspace, cost center, or team through this API.

