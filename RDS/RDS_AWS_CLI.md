# RDS

### List all RDS instances

---

<details>
<summary>Show commands / answers</summary>
<p>

```bash
aws rds describe-db-instances --output table


# Response:
|        DescribeDBInstances             |
+----------------------+--------+--------+
| mydb-instance        | mysql  | 8.0.32 |
| analytics-db         | postgres | 15.2 |
+----------------------+--------+--------+
```

</p>
</details>

### Show engine version of a specific RDS instance

---

<details>
<summary>Show commands / answers</summary>
<p>
  
```bash
aws rds describe-db-instances \
    --db-instance-identifier mydb-instance \
    --query "DBInstances[*].[DBInstanceIdentifier,Engine,EngineVersion]" \
    --output table

# Response:
-------------------------------
| DescribeDBInstances         |
+----------------+--------+----+
| mydb-instance  | mysql  | 8.0.32 |
+----------------+--------+----+
```

</p>
</details>

### Check status of all RDS instances

---

<details>
<summary>Show commands / answers</summary>
<p>
  
```bash
aws rds describe-db-instances \
    --query "DBInstances[*].[DBInstanceIdentifier,DBInstanceStatus]" \
    --output table

# Response:
-----------------------------
| DescribeDBInstances       |
+----------------+---------+
| mydb-instance  | available |
| analytics-db   | backing-up |
+----------------+---------+
```

</p>
</details>

### Get RDS instance endpoint and port

---

<details>
<summary>Show commands / answers</summary>
<p>

```bash
aws rds describe-db-instances \
    --db-instance-identifier mydb-instance \
    --query "DBInstances[*].[Endpoint.Address,Endpoint.Port]" \
    --output table

# Response:
-------------------------
| DescribeDBInstances   |
+----------------+------+
| mydb-instance  | 3306 |
+----------------+------+
```

</p>
</details>

### List tags of an RDS instance

---

<details>
<summary>Show commands / answers</summary>
<p>


```bash
aws rds list-tags-for-resource \
    --resource-name arn:aws:rds:us-east-1:123456789012:db:mydb-instance

# Response:
{
    "TagList": [
        {"Key": "Environment", "Value": "Production"},
        {"Key": "Project", "Value": "Analytics"}
    ]
}
```

</p>
</details>

### Describe available RDS engine versions

---

<details>
<summary>Show commands / answers</summary>
<p>
  
```bash
aws rds describe-db-engine-versions --output table

# Response:
---------------------------------------------
|       DescribeDBEngineVersions            |
+--------+-----------+---------------------+
| mysql  | 8.0.32    | default engine      |
| mysql  | 8.0.33    | default engine      |
| postgres | 15.2    | default engine      |
+--------+-----------+---------------------+
```

</p>
</details>

### Create a new RDS instance

---


<details>
<summary>Show commands / answers</summary>
<p>

```bash
aws rds create-db-instance \
    --db-instance-identifier mydb-instance \
    --db-instance-class db.t3.micro \
    --engine mysql \
    --allocated-storage 20 \
    --master-username admin \
    --master-user-password MyPassword123

# Response:
{
    "DBInstance": {
        "DBInstanceIdentifier": "mydb-instance",
        "DBInstanceStatus": "creating",
        "Engine": "mysql",
        "EngineVersion": "8.0.32"
    }
}
```

</p>
</details>

### Delete an RDS instance

---

<details>
<summary>Show commands / answers</summary>
<p>

```bash
aws rds delete-db-instance \
    --db-instance-identifier mydb-instance \
    --skip-final-snapshot

# Response
{
    "DBInstance": {
        "DBInstanceIdentifier": "mydb-instance",
        "DBInstanceStatus": "deleting"
    }
}
```

</p>
</details>

### Create a snapshot of an RDS instance

---

<details>
<summary>Show commands / answers</summary>
<p>

```bash
aws rds create-db-snapshot \
    --db-instance-identifier mydb-instance \
    --db-snapshot-identifier mydb-snapshot

# Response:
{
    "DBSnapshot": {
        "DBSnapshotIdentifier": "mydb-snapshot",
        "DBInstanceIdentifier": "mydb-instance",
        "Status": "creating"
    }
}
```

</p>
</details>

### List snapshots of an RDS instance

---

<details>
<summary>Show commands / answers</summary>
<p>

```bash
aws rds describe-db-snapshots \
    --db-instance-identifier mydb-instance --output table

# Response:
--------------------------------------
|       DescribeDBSnapshots           |
+----------------------+--------+
| mydb-snapshot        | creating |
+----------------------+--------+

```

</p>
</details>

### Restore an RDS instance from snapshot

---


<details>
<summary>Show commands / answers</summary>
<p>


```bash
aws rds restore-db-instance-from-db-snapshot \
    --db-instance-identifier mydb-restored \
    --db-snapshot-identifier mydb-snapshot

# Response:
{
    "DBInstance": {
        "DBInstanceIdentifier": "mydb-restored",
        "DBInstanceStatus": "restoring"
    }
}
```

</p>
</details>

### Modify an RDS instance (e.g., change class or storage)

---

<details>
<summary>Show commands / answers</summary>
<p>

```bash
aws rds modify-db-instance \
    --db-instance-identifier mydb-instance \
    --db-instance-class db.t3.small \
    --apply-immediately

# Response:
{
    "DBInstance": {
        "DBInstanceIdentifier": "mydb-instance",
        "DBInstanceClass": "db.t3.small",
        "DBInstanceStatus": "modifying"
    }
}
```

</p>
</details>

### Enable Multi-AZ deployment

---

<details>
<summary>Show commands / answers</summary>
<p>

```bash
aws rds modify-db-instance \
    --db-instance-identifier mydb-instance \
    --multi-az \
    --apply-immediately

# Response:
{
    "DBInstance": {
        "DBInstanceIdentifier": "mydb-instance",
        "MultiAZ": true,
        "DBInstanceStatus": "modifying"
    }
}
```

</p>
</details>

### Enable storage encryption

---

<details>
<summary>Show commands / answers</summary>
<p>

```bash
aws rds modify-db-instance \
    --db-instance-identifier mydb-instance \
    --storage-encrypted \
    --apply-immediately

Response:
{
    "DBInstance": {
        "DBInstanceIdentifier": "mydb-instance",
        "StorageEncrypted": true,
        "DBInstanceStatus": "modifying"
    }
}
```

</p>
</details>

### Enable RDS instance monitoring (CloudWatch Enhanced Monitoring)

---

<details>
<summary>Show commands / answers</summary>
<p>

```bash
aws rds modify-db-instance \
    --db-instance-identifier mydb-instance \
    --monitoring-interval 60 \
    --monitoring-role-arn arn:aws:iam::123456789012:role/rds-monitoring-role \
    --apply-immediately


# Response:
{
    "DBInstance": {
        "DBInstanceIdentifier": "mydb-instance",
        "MonitoringInterval": 60,
        "DBInstanceStatus": "modifying"
    }
}
```

</p>
</details>

### Describe parameter groups

---


<details>
<summary>Show commands / answers</summary>
<p>

```bash
aws rds describe-db-parameter-groups --output table

# Response:
--------------------------------------
|      DescribeDBParameterGroups      |
+----------------------+--------+
| default.mysql8.0     | available |
+----------------------+--------+

```

</p>
</details>

### Describe security groups attached to an RDS instance

---


<details>
<summary>Show commands / answers</summary>
<p>

```bash
aws rds describe-db-instances \
    --db-instance-identifier mydb-instance \
    --query "DBInstances[*].VpcSecurityGroups[*].VpcSecurityGroupId" \
    --output table

# Response:
-------------------------------
| VpcSecurityGroupId         |
+----------------+-----------+
| sg-0a1b2c3d4e5f6g7h        |
+----------------+-----------+

```

</p>
</details>

### Delete a snapshot

---

<details>
<summary>Show commands / answers</summary>
<p>

```bash
aws rds delete-db-snapshot --db-snapshot-identifier mydb-snapshot
```

</p>
</details>
