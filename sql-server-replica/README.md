# SQL Server Replica

Information regarding a SQL Server replica within Cloud SQL.

There are two forms of data replication:

## Setting an instance to highly available (zonal availability)

Provides a single instance with multiple zones that can failover at any point.
This automatically occurs on a zone failure.

**Manually failing over will cause ~2-3 minute outage**

## Dedicated read replicas (regional availability)

**Requires SQL Server Enterprise Edition**

Copies of a SQL instance that absorb some of the database read commands from the
main instance to potentially improve performance.

* Can be a different region to allow for cross-region capabilities.
* Read replicas do not support high availability.

## Getting Started

**Initialization**
1. In GCP Console, create a SQL Server instance.
    1. Select SQL Server Enterprise if read replicas desired.
    1. Disable deletion protection if this SQL instance is temporary.
2. Create a database for use.
3. Log in to the instance using Cloud SQL Studio.
4. Create a table to read/write to, and add data:
```
CREATE TABLE Categories (
    ID int NOT NULL PRIMARY KEY IDENTITY(1,1),
    Name varchar(255) NOT NULL
);

INSERT INTO Categories (Name)
VALUES ('Dining Out')
```

**Manual Failover**
1. In console, note the current zone within Configuration.
2. select 'Failover'.
3. Wait for completion _(~1-3 minutes)_.
4. Confirm the zone was changed after failover.

**Read replica - creation, manipulation**
1. Create a read replica through the primary SQL instance.
    1. Use a different region than the primary.
2. Wait for replica creation to finish _(~10 minutes)_
3. Log in to replica with the same credentials as primary instance.
    1. Suggested to have two tabs open with Cloud SQL Studio connected to the
    primary instance and read rep
    ntlica.
4. Add a row to the table above in the primary instance:
```
INSERT INTO Categories (Name)
VALUES ('Groceries')
```
5. Confirm the new row is available in the read replica.
```
SELECT TOP 1000 * FROM
  "dbo"."Categories";
```
6. Attempt to add a row to the table using the read replica - **this will fail
since the read replica is read-only**

**Read replica - promotion**

Promotion converts the read replica to a primary instance. Note that the original
primary instance will stay intact, and you'll have two primary instances

1. Within the read replica in the console, select 'Promote'

**Promotion is a permanent action - once promoted, the read replica becomes a primary instance and cannot be switched back**

### Reference

* [High Availability Info](https://cloud.google.com/sql/docs/sqlserver/high-availability)
* [Regional Failure Recovery](https://medium.com/google-cloud/cloud-sql-recovering-from-regional-failure-in-10-minutes-or-less-mysql-fc055540a8f0)