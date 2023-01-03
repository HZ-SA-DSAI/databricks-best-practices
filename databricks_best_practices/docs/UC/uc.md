# Unity Catalog

1. [Configure a Unity Catalog metastore](https://docs.databricks.com/data-governance/unity-catalog/best-practices.html#configure-a-unity-catalog-metastore)

    1. The following diagram illustrates the main securable objects in Unity Catalog:
    ![UC objects](https://docs.databricks.com/_images/object-model.png)
    2. You create a single metastore in each region you operate and link it to all workspaces in that region. Therefore, if you have multiple regions using Databricks, you will have multiple metastores. To share data between metastores, see [Delta Sharing](https://docs.databricks.com/data-governance/unity-catalog/best-practices.html#delta-sharing).
    3. Each metastore is configured with a root storage location, which is used for managed tables. You need to ensure that no users have direct access to this storage location. You should not reuse a bucket that is your current DBFS root file system or has previously been a DBFS root file system for the root storage location in your Unity Catalog metastore.

2. [External locations and storage credentials](https://docs.databricks.com/data-governance/unity-catalog/best-practices.html#external-locations-and-storage-credentials)

    1. A storage credential encapsulates a long-term cloud credential that provides access to cloud storage. For example, in AWS you can configure an IAM role to access S3 buckets.
    2. An external location is an object that combines a cloud storage path with a storage credential in order to authorize access to the cloud storage path.
    3. Databricks recommends using external locations rather than using storage credentials directly. You should ensure that a limited number of users have direct access to a bucket that is being used as an external location. This is to limit users from bypassing access control in a Unity Catalog metastore and disrupting auditability. For these reasons, you should not mount storage accounts to DBFS that are being used as external locations.
    4. Databricks recommends migrating mounts on cloud storage locations to external locations within Unity Catalog using [Data Explorer](https://docs.databricks.com/data/index.html).

3. [Organize your data](https://docs.databricks.com/data-governance/unity-catalog/best-practices.html#organize-your-data)

    1. ![Data Organization](https://docs.databricks.com/_images/uc-catalogs.png)
    2. Databricks recommends using managed tables whenever possible to ensure support of Unity Catalog features.
        1. Managed tables are the default way to create tables in Unity Catalog. 
        2. These tables are stored in the Unity Catalog root storage location that you configured when you created a metastore. 
        3. All managed tables use Delta Lake.
    3. External tables are a good option for providing direct access to raw data
        1. External tables are tables whose data is stored in a storage location outside of the managed storage location. 
        2. They aren’t fully managed by Unity Catalog. 
        3. External tables support Delta Lake and many other data formats, including Parquet, JSON, and CSV.

4. [Manage external locations and external tables](https://docs.databricks.com/data-governance/unity-catalog/best-practices.html#manage-external-locations-and-external-tables)

    1. The diagram below represents the filesystem hierarchy of a single cloud storage bucket:
        ![Cloud Bucket](https://docs.databricks.com/_images/external-locations-aws.png)
        
        There are four external locations created and one storage credential used by them all. Users and groups can be granted access to the different storage locations within a Unity Catalog metastore. This allows you to provide specific groups access to different part of the cloud storage bucket.

    2. Databricks recommends that you create external tables from one storage location within one schema.

        You can create external tables using a storage location in a Unity Catalog metastore. Those external tables can then be secured independently.

    3. Databricks strongly does not recommend registering common tables as external tables in more than one metastore due to the risk of consistency issues.

         For example, a change to the schema in one metastore will not register in the second metastore. Use Delta Sharing for sharing data between metastores. [See Delta Sharing.](https://docs.databricks.com/data-governance/unity-catalog/best-practices.html#delta-sharing)

5. [Configure access control](https://docs.databricks.com/data-governance/unity-catalog/best-practices.html#configure-access-control)

    1. It is best practice to configure ownership on all objects to the group responsible for administration of grants on the object.

        Both the owner and metastore admins can transfer ownership of a securable object to a group. Additionally, if the object is contained within a catalog (like a table or view), the catalog and schema owner can change the ownership of the object.

    2. Securable objects in Unity Catalog are hierarchical and privileges are inherited downward. This means that granting a privilege on a catalog or schema automatically grants the privilege to all current and future objects within the catalog or schema. For more information, see [Inheritance model.](https://docs.databricks.com/data-governance/unity-catalog/manage-privileges/privileges.html#inheritance)

    3. In order to read data from a table or view a user must have the following privileges:
        1. SELECT on the table or view
        2. USE SCHEMA on the schema that owns the table
        3. USE CATALOG on the catalog that owns the schema

        You can use this privilege to restrict access to sections of your data namespace to specific groups. A common scenario is to set up a schema per team where only that team has USE SCHEMA and CREATE on the schema. This means that any tables produced by team members can only be shared within the team.

6. [Manage cluster configurations](https://docs.databricks.com/data-governance/unity-catalog/best-practices.html#manage-cluster-configurations)

    1. Databricks recommends using cluster policies to limit the ability to configure clusters based on a set of rules. 
        1. Using cluster policies reduces available choices, which will greatly simplify the cluster creation process for users and ensure that they are able to access data seamlessly. 
        2. Cluster policies also enable you to control cost by limiting per cluster maximum cost.

    2. Unity Catalog is secure by default; if a cluster is not configured with an appropriate access mode, the cluster can’t access data in Unity Catalog. [See Cluster access modes for Unity Catalog.](https://docs.databricks.com/data-governance/unity-catalog/index.html#cluster-security-mode)

    3. Databricks recommends using the User Isolation access mode when sharing a cluster and the Single User access mode for automated jobs and machine learning workloads.

7. [Audit access](https://docs.databricks.com/data-governance/unity-catalog/best-practices.html#audit-access)

    1. Unity Catalog captures an audit log of actions performed against the metastore and these logs are delivered as part of Databricks audit logs.
    2. Make sure you [configure audit logging](https://docs.databricks.com/administration-guide/account-settings/audit-logs.html). This involves configuring the right access policy so that Databricks can deliver audit logs to an S3 bucket that you provide. Audit logs are typically logged within 15 minutes. Databricks recommends sharing the same audit log configuration for all workspaces in the account.
    3. See [Monitoring Your Databricks Lakehouse Platform with Audit Logs](https://www.databricks.com/blog/2022/05/02/monitoring-your-databricks-lakehouse-platform-with-audit-logs.html?_ga=2.148065015.152536432.1672769337-917306584.1665418964) for details on how to get complete visibility into critical events relating to your Databricks Lakehouse Platform.

8. [Delta Sharing](https://docs.databricks.com/data-governance/unity-catalog/best-practices.html#delta-sharing-1)

    1. To share data between metastores, you can leverage [Databricks-to-Databricks Delta Sharing](https://docs.databricks.com/data-sharing/index.html#d-to-d). 
    2. This allows you to register tables from metastores in different regions. These tables will appear as read-only objects in the consuming metastore. These tables can be granted access like any other object within Unity Catalog.
    3. When you use Databricks-to-Databricks Delta Sharing to share between metastores, keep in mind that access control is limited to one metastore. If a securable object, like a table, has grants on it and that resource is shared to an intra-account metastore, then the grants from the source will not apply to the destination share. The destination share will have to set its own grants.