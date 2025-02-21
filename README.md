# Coalesce Base Node Types Package

The Coalesce Base Node Types Package includes:

* [Work](#work)
* [Persistent Stage](#persistent-stage)
* [Dimension](#dimension)
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
| **Rename Table\| Alter Column \| Delete Column \| Add Column \|**| Alter table statement is executed to perform the alter operation |
| **Rename clone Table** | Upon successful completion of all updates, the clone replaces the main table ensuring that no data is lost |
| **Delete Table** | Drops the internal table |

#### Recreating the Work View

If any of the following change are detected, then the table will be recreated by DROP View first and then CREATE.

* Join clause
* Adding transformation
* Changes in configuration like adding distinct.

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

## Persistent Stage 

The Coalesce Persistent Stage Nodes element, serving as an intermediary object, is frequently utilized to maintain data persistence across multiple execution cycles.

It plays a crucial role in tracking the historical changes of columns linked to business keys.

This functionality is particularly beneficial when the objective is to retain raw data for prolonged durations.

### Persistent Stage Node Configuration

The Persistent node type has two configuration groups:

* [Node Properties](#persistent-stage-node-properties)
* [Options](#persistent-stage-options)

![Fact_config](https://github.com/coalesceio/Coalesce-Base-Node-Types/assets/7216836/6f863c3b-3abd-4318-bd7e-ed50a829f911)

#### Persistent Stage Node Properties

| **Property** | **Description** |
|----------|-------------|
| **Storage Location** | Storage Location where the WORK will be created |
| **Node Type** | Name of template used to create node objects |
| **Description** | A description of the node's purpose |
| **Deploy Enabled** | If TRUE the node will be deployed / redeployed when changes are detected<br/> If FALSE the node will not be deployed or will be dropped during redeployment |

#### Persistent Stage Options

![image](https://github.com/user-attachments/assets/21c63618-a311-4102-a3c0-ef783282b1ba)

| **Option** | **Description** |
|---------|-------------|
| **Create As** | Table is the only option at this time |
| **Multi Source** | Toggle: True/False<br/>Implementation of SQL UNIONs<br/>**True**: Combine multiple sources in a single node<br/>True Options:<br/>- **UNION**: Combines with duplicate elimination<br/>- **UNION ALL**: Combines without duplicate elimination<br/>**False**: Single source node or multiple sources combined using a join |
| **Business key** | Required column for both Type 1 and Type 2 |
| **Change tracking** | Required column for Type 2 |
| **Truncate Before** | Toggle: True/False<br/>This determines whether a table will be overwritten each time a task executes.<br/> **True**: Uses INSERT OVERWRITE<br/>**False**: Uses INSERT to append data |
| **Enable tests** | Toggle: True/False<br/>Determines if tests are enabled |
| **Distinct** | Toggle: True/False<br/>**True**: Group by All is invisible. DISTINCT data is chosen for processing<br/>**False**: Group by All is visible |
| **Pre-SQL** | SQL to execute before data insert operation |
| **Post-SQL** | SQL to execute after data insert operation |

### Persistent Stage Joins

Join conditions and other clauses can be specified in the join space next to mapping of columns in the UI.

![pstage_join](https://github.com/coalesceio/Coalesce-Base-Node-Types/assets/7216836/84ce06c9-103c-4700-ad3d-2e8222995b31)

> 📘 **Specify Group by and Order by Clauses**
>
> You should specify group by and order by clauses in this space if you are not opting for the group by all and order by provided in OPTIONS config.

### Persistent Stage Deployment

#### Persistent Stage Initial Deployment

When deployed for the first time into an environment the Persistent node will execute the below stage:

| **Stage** | **Description** |
|-----------|----------------|
| **Create Persistent Table** | This will execute a CREATE OR REPLACE statement and create a table in the target environment |

#### Persistent Stage Redeployment

After the Persistent node has been deployed for the first time into a target environment, subsequent deployments may result in either altering the Persistent Table or recreating the Persistent table.

#### Altering the Persistent Tables

A few types of column or table changes will result in an ALTER statement to modify the Persistent Table in the target environment, whether these changes are made individually or all together:

* Changing table names
* Dropping existing columns
* Altering column data types
* Adding new columns

The following stages are executed:

| **Stage** | **Description** |
|-----------|----------------|
| **Clone Table** | Creates an internal table |
| **Rename Table\| Alter Column \| Delete Column \| Add Column \| Rename Column** | Alter table statement is executed to perform the alter operation |
| **Rename Clone Table** | Upon successful completion of all updates, the clone replaces the main table ensuring that no data is lost |
| **Delete Table** | Drops the internal table |

### Persistent Stage Undeployment

If a Persistent Node is deleted from a Workspace, that Workspace is committed to Git and that commit deployed to a higher level environment then the Persistent Table in the target environment will be dropped.

This is executed in two stages:

| **Stage** | **Description** |
|-----------|----------------|
| **Delete Table** | Drops table |
| **Drop Table or View** | Removes the table |

---
## Dimension

The Coalesce Dimension UDN is a versatile node that allows you to develop and deploy a Dimension table in Microsoft Fabric.

A dimension table or dimension entity is a table or entity in a star, snowflake, or starflake schema that stores details about the facts. Dimension tables describe the different aspects of a business process.

### Dimension Node Configuration

The Dimension node type has two configuration groups:

* [Node Properties](#dimension-node-properties)
* [Options](#dimension-options)

![Fact_config](https://github.com/coalesceio/Coalesce-Base-Node-Types/assets/7216836/6f863c3b-3abd-4318-bd7e-ed50a829f911)

#### Dimension Node Properties

| **Property** | **Description** |
|----------|-------------|
| **Storage Location** | Storage Location where the WORK will be created |
| **Node Type** | Name of template used to create node objects |
| **Description** | A description of the node's purpose |
| **Deploy Enabled** | If TRUE the node will be deployed / redeployed when changes are detected<br/> If FALSE the node will not be deployed or will be dropped during redeployment |

#### Dimension Options

##### Dimension Table Options

![Dimension](https://github.com/user-attachments/assets/8f65ee5c-ce09-457e-b4de-9e6c94038795)

| **Options** | **Description** |
|---------|-------------|
| **Create As** | Table or View |
| **Multi Source** | Toggle: True/False<br/>Implementation of SQL UNIONs<br/>**True**: Combine multiple sources in a single node<br/>True Options:<br/>- **UNION**: Combines with duplicate elimination<br/>- **UNION ALL**: Combines without duplicate elimination<br/>**False**: Single source node or multiple sources combined using a join |
| **Business key** | Required column for both Type 1 and Type 2 Dimensions |
| **Change tracking** | Required column for Type 2 Dimension |
| **Truncate Before** | Toggle: True/False<br/>This determines whether a table will be overwritten each time a task executes. **True**: Uses INSERT OVERWRITE<br/>**False**: Uses INSERT to append data |
| **Enable tests** | Toggle: True/False<br/>Determines if tests are enabled |
| **Distinct** | Toggle: True/False<br/>**True**: Group by All is invisible. DISTINCT data is chosen for processing<br/>**False**: Group by All is visible |
| **Pre-SQL** | SQL to execute before data insert operation |
| **Post-SQL** | SQL to execute after data insert operation |

##### Dimension View Options

![Work_options_view1](https://github.com/coalesceio/Coalesce-Base-Node-Types/assets/7216836/7881c46e-0424-46ca-9a89-d111b5dbc379)

| **Options** | **Description** |
|---------|-------------|
| **Override Create SQL** | Toggle: True/False<br/>**True**: Customized Create SQL specified in the Create SQL space is executed. All other options are invisible<br/>**False**: Create view SQL based on options chosen are framed and executed |
| **Multi Source** | Toggle: True/False<br/>Implementation of SQL UNIONs<br/>**True**: Combine multiple sources in a single node<br/>True Options:<br/>- **UNION**: Combines with duplicate elimination<br/>- **UNION ALL**: Combines without duplicate elimination<br/>**False**: Single source node or multiple sources combined using a join |
| **Business key** | Required column for both Type 1 and Type 2 Dimensions |
| **Distinct** | Toggle: True/False<br/>**True**: Group by All is invisible. DISTINCT data is chosen for processing<br/>**False**: Group by All is visible |

### Dimension Joins

Join conditions and other clauses can be specified in the join space next to mapping of columns in the UI.

![Dimension_join](https://github.com/coalesceio/Coalesce-Base-Node-Types/assets/7216836/5c3df3b0-f56d-4276-a51f-22364206b3c3)

### Dimension Deployment

#### Dimension Initial Deployment

When deployed for the first time into an environment the Dimension node of materialization type table will execute the Create Dimension Table stage.

| **Stage** | **Description** |
|-----------|----------------|
| **Drop Dimension Table** | This will execute a DROP statement and Drops a table in the target environment |
| **Create Dimension Table** | This will execute a CREATE statement and create a table in the target environment |
| **Drop Dimension View** | This will execute a DROP statement and Drops a view in the target environmentt |
| **Create Dimension View** | This will execute a CREATE statement and create a view in the target environment |

#### Dimension Redeployment

After the Dimension node of materialization type table has been deployed for the first time into a target environment, subsequent deployments may result in either altering the Dimension Table or recreating the Dimension table.

#### Altering the Dimension Tables

A few types of column or table changes will result in an ALTER statement to modify the Dimension Table in the target environment, whether these changes are made individually or all together:

* Changing table names
* Dropping existing columns
* Altering column data types
* Adding new columns

The following stages are executed:

| **Stage** | **Description** |
|-----------|----------------|
| **Clone Table** | Creates an internal table |
| **Rename Table\| Alter Column \| Delete Column \| Add Column \|** | Alter table statement is executed to perform the alter operation |
| **Rename Clone Table** | Upon successful completion of all updates, the clone replaces the main table ensuring that no data is lost |
| **Delete Table** | Drops the internal table |

#### Recreating the Dimension Views

Any of the following changes to views will result in deleting and recreating the Dimension view.

* View defintion
* Renaming view results

### Dimension Undeployment

If a Dimension Node is deleted from a Workspace, that Workspace is committed to Git and that commit deployed to a higher level environment then the Dimension Table in the target environment will be dropped.

This is executed in two stages:

| **Stage** | **Description** |
|-----------|----------------|
| **Delete Table** | Coalesce Internal table is dropped |
| **Delete Table** | Target table in Microsoft Fabric is dropped |

If a Dimension Node of materialization type view is deleted from a Workspace, that Workspace is committed to Git and that commit deployed to a higher level environment then the Dimension View in the target environment will be dropped.

| **Stage** | **Description** |
|-----------|----------------|
| **Delete View** | Drops the existing Dimension view from target environment. |
---

### Work Code

* [Node definition](https://github.com/coalesceio/fabric-nodes/blob/main/nodeTypes/Work-147/definition.yml)
* [Create Template](https://github.com/coalesceio/fabric-nodes/blob/main/nodeTypes/Work-147/create.sql.j2)
* [Run Template](https://github.com/coalesceio/fabric-nodes/blob/main/nodeTypes/Work-147/run.sql.j2)

### Persistent Stage Code

* [Node definition](https://github.com/coalesceio/Coalesce-Base-Node-Types/blob/main/nodeTypes/PersistentStage-173/definition.yml)
* [Create Template](https://github.com/coalesceio/Coalesce-Base-Node-Types/blob/main/nodeTypes/PersistentStage-173/create.sql.j2)
* [Run Template](https://github.com/coalesceio/Coalesce-Base-Node-Types/blob/main/nodeTypes/PersistentStage-173/run.sql.j2)

### Dimension Code

* [Node definition](https://github.com/coalesceio/fabric-nodes/blob/main/nodeTypes/Dimension_10-148/definition.yml)
* [Create Template](https://github.com/coalesceio/fabric-nodes/blob/main/nodeTypes/Dimension_10-148/create.sql.j2)
* [Run Template](https://github.com/coalesceio/fabric-nodes/blob/main/nodeTypes/Dimension_10-148/run.sql.j2)


[Macros](https://github.com/coalesceio/fabric-nodes/blob/main/macros/macro-1.yml).
