***
# Identity

**Updated on 01/05/2023**

*See below for original doc on docs.databricks.com*

*[Identity best practices](https://docs.databricks.com/administration-guide/users-groups/best-practices.html#identity-best-practices)*

1. [Configure users, service principals, and groups](https://docs.databricks.com/administration-guide/users-groups/best-practices.html#configure-users-service-principals-and-groups)
    1. Three types of Databricks identity:
        1. Users: User identities recognized by Databricks and represented by email addresses.
        2. Service principals: Identities for use with jobs, automated tools, and systems such as scripts, apps, and CI/CD platforms.
        3. Groups: Groups simplify identity management, making it easier to assign access to workspaces, data, and other securable objects.
    
    2. Databricks recommends creating service principals to run production jobs or modify production data. 

        If all processes that act on production data run with service principals, interactive users do not need any write, delete, or modify privileges in production. This eliminates the risk of a user overwriting production data by accident.

    3. It is best practice to assign access to workspaces and access-control policies in Unity Catalog to groups, instead of to users individually.

        All Databricks identities can be assigned as members of groups, and members inherit permissions that are assigned to their group.

    4. Three types of administrative roles for managing Databricks:
        1. Account admins can manage your Databricks account-level configurations including identity, billing, cloud resources, and the creation of workspaces and Unity Catalog metastores.
        2. Workspace admins can add users to a Databricks workspace, assign them the workspace admin role, and manage access to objects and functionality in the workspace, such as the ability to create clusters and change job ownership.
        3. Metastore admins can manage privileges for all securable objects within a metastore, such as who can create catalogs or query a table. The account admin who creates the Unity Catalog metastore becomes the initial metastore admin.

    5. Databricks recommends that there should be a limited number of account admins per account and workspace admins in each workspace. It is a best practice to transfer the metastore admin role to a group. See [(Recommended) Transfer ownership of your metastore to a group](https://docs.databricks.com/data-governance/unity-catalog/get-started.html#transfer-ownership)

2. [Sync users and groups from your identity provider to your Databricks account](https://docs.databricks.com/administration-guide/users-groups/best-practices.html#sync-users-and-groups-from-your-identity-provider-to-your-databricks-account)

    1. Databricks recommends using SCIM provisioning to sync users and groups automatically from your identity provider to your Databricks account.

        SCIM streamlines onboarding a new employee or team by using your identity provider to create users and groups in Databricks and give them the proper level of access. 

    2. You should aim to synchronize all of the users and groups that intend to use Databricks to the account console rather than individual workspaces.

        This way you only need to configure one SCIM provisioning application to keep all identities consistent across all workspaces in the account.

    3. If you already have workspace-level SCIM provisioning set up for workspaces, you should set up account-level SCIM provisioning and turn off the workspace-level SCIM provisioner. [See Upgrade to identity federation.](https://docs.databricks.com/administration-guide/users-groups/best-practices.html#upgrade-to-id-fed)

        ![Identity flow](https://docs.databricks.com/_images/account-level-scim-diagram.png)

3. Configure single sign-on

    1. Databricks recommends configuring SSO for greater security and improved usability.

        1. You must configure SSO on the account and on individual workspaces.
        2. You should configure SSO to the same identity provider for your account and all workspaces in your account.
        3. Preferably, use OIDC for SSO configuration at the account level to ensure support of authentication features.

    2. Once SSO is configured on your workspaces, you should configure [password access control](https://docs.databricks.com/administration-guide/users-groups/single-sign-on/index.html#password). 

        Password access control enables you to restrict users from authenticating to REST APIs with their usernames and password. Instead, users must authenticate to REST APIs using [personal access tokens](https://docs.databricks.com/dev-tools/api/latest/authentication.html). 

    3. Databricks recommends that you do not grant Can Use passwords to any workspace users.

4. [Enable identity federation](https://docs.databricks.com/administration-guide/users-groups/best-practices.html#enable-identity-federation)

    1. Identity federation enables you to configure users, service principals, and groups in the account console, and then assign those identities access to specific workspaces. This simplifies Databricks administration and data governance.

    2. With identity federation, you configure Databricks users, service principals, and groups once in the account console, rather than repeating configuration separately in each workspace. 

        This both reduces friction in onboarding a new team to Databricks and enables you to maintain one SCIM provisioning application with your identity provider to the Databricks account, instead of a separate SCIM provisioning application for each workspace. Once users, service principals, and groups are added to the account, you can assign them permissions on workspaces.

    3. You can only assign account-level identities access to workspaces that are enabled for identity federation.

        ![Identity Federation](https://docs.databricks.com/_images/account-level-identity-diagram.png)

    4. Identity federation is enabled on the workspace-level and you can have a combination of identity federated and non-identity federated workspaces.

        For those workspaces that are not enabled for identity federation, workspace admins manage their workspace users, service principals, and groups entirely within the scope of the workspace (the legacy model). They cannot use the account console or account-level APIs to assign users from the account to these workspaces, but they can use any of the workspace-level interfaces. Whenever a new user or service principal is added to a workspace using workspace-level interfaces, that user or service principal is synchronized to the account-level. This enables you to have one consistent set of users and service principals in your account.
    
    5. However, when a group is added to a non-identity-federated workspace using workspace-level interfaces, that group is a [workspace-local group](https://docs.databricks.com/administration-guide/users-groups/index.html#special-groups) and is not added to the account.

        Account groups can be created only by account admins using account-level interfaces. You should aim to use account groups rather than workspace-local groups. Workspace-local groups cannot be granted access-control policies in Unity Catalog or permissions to other workspaces.

5. [Upgrade to identity federation](https://docs.databricks.com/administration-guide/users-groups/best-practices.html#upgrade-to-identity-federation)

    1. Migrate workspace-level SCIM provisioning to the account level

        If you have a workspace-level SCIM provisioning set up your workspace, you should set up account-level SCIM provisioning and turn off the workspace-level SCIM provisioner.

        Databricks recommends using account groups instead of workspace-local groups to take advantage of centralized workspace assignment and data access management using Unity Catalog.

    2. Convert workspace-local groups to account groups

        Databricks recommends converting your existing workspace-local groups to account groups. 

6. [Assign groups workspace permissions](https://docs.databricks.com/administration-guide/users-groups/best-practices.html#assign-groups-workspace-permissions)

    Databricks recommends that you assign groups permissions to workspaces, instead assigning workspace permissions to users individually. All Databricks identities can be assigned as members of groups, and members inherit permissions that are assigned to their group.