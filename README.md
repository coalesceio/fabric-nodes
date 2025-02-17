# Coalesce Base Node Types Package

The Coalesce Base Node Types Package includes:

* [Work](#work)
* [Code](#code)

---

## Work

The Coalesce work node is a versatile node that allows you to develop and deploy a Work table/view in Microsoft Fabric.

A Work node serves as an intermediary object and is commonly employed to store raw data before undergoing the crucial phases of transformation and loading into the main tables of the data warehouse.

This pivotal step ensures that the raw data is processed and structured effectively.

### Work Node Configuration

The Work node type has two configuration groups:

* [Node Properties](#work-node-properties)
* [Options](#work-options)

![Fact_config](https://github.com/coalesceio/Coalesce-Base-Node-Types/assets/7216836/6f863c3b-3abd-4318-bd7e-ed50a829f911)

#### Work Node Properties

| **Property** | **Description** |
|----------|-------------|
| **Storage Location** | Storage Location where the WORK will be created |
| **Node Type** | Name of template used to create node objects |
| **Description** | A description of the node's purpose |
| **Deploy Enabled** | If TRUE the node will be deployed / redeployed when changes are detected<br/> If FALSE the node will not be deployed or will be dropped during redeployment |

#### Work Options

You can create the node as:

* [Table](#work-options-table)
* [View](#work-options-view)

##### Work Options Table

<img width="303" alt="image" src="https://github.com/user-attachments/assets/b671e6c0-4fc1-4c78-b181-89e8703868b9" />


| **Property** | **Description** |
|---------|-------------|
| **Multi Source** | Toggle: True/False<br/>Implementation of SQL UNIONs<br/>**True**: Combine multiple sources in a single node<br/>True Options:<br/>- **UNION**: Combines with duplicate elimination<br/>- **UNION ALL**: Combines without duplicate elimination<br/>- **INSERT**: Individual insert for each source<br/>**False**: Single source node or multiple sources combined using a join |
| **Truncate Before** | Toggle: True/False<br/>This determines whether a table will be overwritten each time a task executes.<br/>**True**: Uses INSERT OVERWRITE<br/>**False**: Uses INSERT to append data |
| **Enable tests** | Toggle: True/False<br/>Determines if tests are enabled |
| **Distinct** | Toggle: True/False<br/>**True**: DISTINCT data is chosen for processing<br/>**False**: Only Select without distinct is executed|
| **Pre-SQL** | SQL to execute before data insert operation |
| **Post-SQL** | SQL to execute after data insert operation |

##### Work Options View

![image](https://github.com/user-attachments/assets/6c18d488-03df-4073-8932-75c6ae1e7d11)



| **Setting** | **Description** |
|---------|-------------|
| **Override Create SQL** | Toggle: True/False<br/>**True**: Customized Create SQL specified in the Create SQL space is executed. All other options are invisible<br/>**False**: Create view SQL based on options chosen are framed and executed |
| **Multi Source** | Toggle: True/False<br/>Implementation of SQL UNIONs<br/>**True**: Combine multiple sources in a single node<br/>True Options:<br/>- **UNION**: Combines with duplicate elimination<br/>- **UNION ALL**: Combines without duplicate elimination<br/>**False**: Single source node or multiple sources combined using a join |
| **Distinct** | Toggle: True/False<br/>**True**: Group by All is invisible. DISTINCT data is chosen for processing<br/>**False**: Group by All is visible |


### Work Joins

Join conditions and other clauses can be specified in the join space next to mapping of columns in the UI.

![work_join](https://github.com/coalesceio/Coalesce-Base-Node-Types/assets/7216836/2dc81bb8-2285-46e1-8b93-ce7082800fc5)

### Work Deployment

#### Work Initial Deployment

When deployed for the first time into an environment the Work node of materialization type table will execute the below stage:

| **Stage** | **Description** |
|-----------|----------------|
| **Drop Work Table** | This will execute a DROP statement and Drops a table in the target environment |
| **Create Work Table** | This will execute a CREATE statement and create a table in the target environment |

When deployed for the first time into an environment the Work node of materialization type view will execute the below stage:

| **Stage** | **Description** |
|-----------|----------------|
| **Drop Work View** | This will execute a DROP statement and Drops a view in the target environment |
| **Create Work View** | This will execute a CREATE statement and create a view in the target environment |

#### Work Redeployment

After the WORK node with materialization type table has been deployed for the first time into a target environment, subsequent deployments may result in either altering the WORK Table or recreating the WORK table.

#### Altering the Work Tables

A few types of column or table changes will result in an ALTER statement to modify the Work Table in the target environment, whether these changes are made individually or all together:

* Changing table names
* Dropping existing columns
* Altering column data types
* Adding new columns

The following stages are executed:

| **Stage** | **Description** |
|-----------|----------------|
| **Clone Table** | Creates an internal table |
| **Rename Table\| Alter Column \| Delete Column \| Add Column \| Edit table description** | Alter table statement is executed to perform the alter operation |
| **Swap Cloned Table** | Upon successful completion of all updates, the clone replaces the main table ensuring that no data is lost |
| **Delete Table** | Drops the internal table |

#### Recreating the Work Tables

If any of the following change are detected, then the table will be recreated by DROP table first and then CREATE table.

* Join clause
* Adding transformation
* Changes in configuration like adding distinct.

One of the following stages are executed:

| **Stage** | **Description** |
|-----------|----------------|
| **Create Table** | Creates a new table |
| **Replace Table** | Replaces an existing table|

#### Recreating the Work Views

The subsequent deployment of Work node of materialization type view with changes in view definition, adding table description or renaming view results in deleting the existing view and recreating the view.

The following stages are executed:

| **Stage** | **Description** |
|-----------|----------------|
| **Delete View** | Removes existing view |
| **Create View** | Creates new view with updated definition |

### Work Undeployment

If a Work Node of materialization type table is deleted from a Workspace, that Workspace is committed to Git and that commit deployed to a higher level environment then the WorkTable in the target environment will be dropped.

This is executed in two stages:

| **Stage** | **Description** |
|-----------|----------------|
| **Delete Table** | Coalesce Internal table is dropped |
| **Delete Table** | Target table in Microsoft Fabric is dropped |

If a Work Node of materialization type view is deleted from a Workspace, that Workspace is committed to Git and that commit deployed to a higher level environment then the WorkView in the target environment will be dropped.

The stage executed:

| **Stage** | **Description** |
|-----------|----------------|
| **Delete View** | Drops the existing Work view from target environment |

---

### Work Code

* [Node definition](https://github.com/coalesceio/fabric-nodes/blob/main/nodeTypes/Work-147/definition.yml)
* [Create Template](https://github.com/coalesceio/fabric-nodes/blob/main/nodeTypes/Work-147/create.sql.j2)
* [Run Template](https://github.com/coalesceio/fabric-nodes/blob/main/nodeTypes/Work-147/run.sql.j2)



[Macros](https://github.com/coalesceio/fabric-nodes/blob/main/macros/macro-1.yml).
