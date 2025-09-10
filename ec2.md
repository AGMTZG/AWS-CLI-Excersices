# EC2

# Basic operations with EC2

### Retrieve a list of all EC2 instances in your environment

---

<details>
<summary>Show commands / answers</summary>
<p>

```bash
aws ec2 describe-instances
```

</p>
</details>

### Launch a new EC2 instance using an Ubuntu AMI with instance type t2.micro

---

<details>
<summary>Show commands / answers</summary>
<p>

```bash
aws ec2 run-instances \
  --image-id <AMI_ID> \
  --count 1 \
  --instance-type t2.micro \
  --key-name <KeyPairName> \
  --security-group-ids <sg-xxxx> \
  --subnet-id <subnet-xxxx>
```

</p>
</details>

### Start a stopped EC2 instance to make it running and accessible

---

<details>
<summary>Show commands / answers</summary>
<p>
  
```bash
aws ec2 start-instances --instance-ids <instance-id>
```

</p>
</details>

### Stop a running EC2 instance to temporarily shut it down while preserving its configuration

---

<details>
<summary>Show commands / answers</summary>
<p>
  
```bash
aws ec2 stop-instances --instance-ids <instance-id>
```

</p>
</details>

### Reboot a running EC2 instance to restart it without changing its configuration or losing data

---

<details>
<summary>Show commands / answers</summary>
<p>
  
```bash
aws ec2 reboot-instances --instance-ids <instance-id>
```

</p>
</details>

### Terminate an EC2 instance to permanently delete it, including its associated resources

---

<details>
<summary>Show commands / answers</summary>
<p>
  
```bash
aws ec2 terminate-instances --instance-ids <instance-id>
```

</p>
</details>

# Key Pairs

### Create a New Key Pair and Connect to an EC2 Instance

---

<details>
<summary>Show commands / answers</summary>
<p>
  
```bash
aws ec2 create-key-pair --key-name MyKey --query 'KeyMaterial' --output text > MyKey.pem
chmod 400 MyKey.pem

ssh -i MyKey.pem <AMi>/<public-instance-ip>
```

</p>
</details>

### List all key pairs in your AWS account to view their names and associated information

---

<details>
<summary>Show commands / answers</summary>
<p>
  
```bash
aws ec2 describe-key-pairs
```

</p>
</details>

# Information of you EC2 instance

### Find the Instance ID of a specific EC2 instance in your AWS account

---

<details>
<summary>Show commands / answers</summary>
<p>

```bash
aws ec2 describe-instances --query "Reservations[*].Instances[*].[InstanceId,Tags[?Key=='Name'].Value]" --output table
```

</p>
</details>

### Find the VPC ID associated with a specific EC2 instance in your AWS account

---

<details>
<summary>Show commands / answers</summary>
<p>

```bash
aws ec2 describe-instances --instance-ids <INSTANCE_ID> --query "Reservations[0].Instances[0].VpcId" --output text
```

</p>
</details>

### Find the Subnet ID associated with a specific EC2 instance in your AWS account

---

<details>
<summary>Show commands / answers</summary>
<p>

```bash
aws ec2 describe-instances --instance-ids <INSTANCE_ID> --query "Reservations[0].Instances[0].SubnetId" --output text
```

</p>
</details>

### Find the Security Group ID(s) attached to a specific EC2 instance in your AWS account

---

<details>
<summary>Show commands / answers</summary>
<p>

```bash
aws ec2 describe-instances --instance-ids <INSTANCE_ID> --query "Reservations[0].Instances[0].SecurityGroups[*].GroupId" --output text
```

</p>
</details>

### Retrieve the public IP address of a specific EC2 instance

---

<details>
<summary>Show commands / answers</summary>
<p>
  
```bash
aws ec2 describe-instances --instance-ids <INSTANCE_ID> --query "Reservations[0].Instances[0].PublicIpAddress" --output text
```

</p>
</details>

### Retrieve the private IP address of a specific EC2 instance

---

<details>
<summary>Show commands / answers</summary>
<p>
  
```bash
aws ec2 describe-instances --instance-ids <instance-id> --query 'Reservations[*].Instances[*].PrivateIpAddress' --output text
```

</p>
</details>

### Retrieve the public DNS of a specific EC2 instance

---

<details>
<summary>Show commands / answers</summary>
<p>
  
```bash
aws ec2 describe-instances --instance-ids <instance-id> --query 'Reservations[*].Instances[*].PublicDnsName' --output text
```

</p>
</details>

# Security Groups

### List all Security Groups

---

<details>
<summary>Show commands / answers</summary>
<p>
  
```bash
aws ec2 describe-security-groups
```

</p>
</details>

### Create a security group

---

<details>
<summary>Show commands / answers</summary>
<p>
  
```bash
aws ec2 create-security-group \
  --group-name MySG \
  --description "My security group" \
  --vpc-id <vpc-id>
```

</p>
</details>

### Add inbound rule

---

<details>
<summary>Show commands / answers</summary>
<p>
  
```bash
ws ec2 authorize-security-group-ingress \
  --group-id <sg-id> \
  --protocol tcp \
  --port 22 \
  --cidr 0.0.0.0/0
```

</p>
</details>

### Add outbound rule

---

<details>
<summary>Show commands / answers</summary>
<p>
  
```bash
aws ec2 authorize-security-group-egress \
  --group-id <sg-id> \
  --protocol tcp \
  --port 80 \
  --cidr 0.0.0.0/0
```

</p>
</details>

# Elastic IPs

### Allocate a new Elastic IP

---

<details>
<summary>Show commands / answers</summary>
<p>
  
```bash
aws ec2 allocate-address
```

</p>
</details>

### Associate Elastic IP with instance

---

<details>
<summary>Show commands / answers</summary>
<p>
  
```bash
aws ec2 associate-address --instance-id <instance-id> --allocation-id <allocation-id>
```

</p>
</details>

### Release Elastic IP

---

<details>
<summary>Show commands / answers</summary>
<p>
  
```bash
aws ec2 release-address --allocation-id <allocation-id>
```

</p>
</details>

# Volumes and EBS

### List volumes

---

<details>
<summary>Show commands / answers</summary>
<p>
  
```bash
aws ec2 describe-volumes
```

</p>
</details>

### Create a new volume

---

<details>
<summary>Show commands / answers</summary>
<p>
  
```bash
aws ec2 create-volume \
  --availability-zone us-east-1a \
  --size 10 \
  --volume-type gp3
```

</p>
</details>

### Attach volume to instance

---

<details>
<summary>Show commands / answers</summary>
<p>
  
```bash
aws ec2 attach-volume \
  --volume-id <volume-id> \
  --instance-id <instance-id> \
  --device /dev/sdf
```

</p>
</details>

### Detach volume

---

<details>
<summary>Show commands / answers</summary>
<p>
  
```bash
aws ec2 detach-volume --volume-id <volume-id>
```

</p>
</details>

### Delete volume

---

<details>
<summary>Show commands / answers</summary>
<p>
  
```bash
aws ec2 delete-volume --volume-id <volume-id>
```

</p>
</details>

# AMIs (Amazon Machine Images)

List all available AMIs

---

<details>
<summary>Show commands / answers</summary>
<p>
  
```bash
aws ec2 describe-images --owners self amazon
```

</p>
</details>

# Tags

List all instance tags

---

<details>
<summary>Show commands / answers</summary>
<p>
  
```bash
aws ec2 describe-tags --filters "Name=resource-id,Values=<instance-id>"
```

</p>
</details>

### Add a tag to an instance

---

<details>
<summary>Show commands / answers</summary>
<p>
  
```bash
aws ec2 create-tags --resources <instance-id> --tags Key=Name,Value=MyInstance
```

</p>
</details>

# Monitoring & Logs

### Get instance status

---

<details>
<summary>Show commands / answers</summary>
<p>
  
```bash
aws ec2 describe-instance-status --instance-ids <instance-id>
```

</p>
</details>

### Enable CloudWatch detailed monitoring

---

<details>
<summary>Show commands / answers</summary>
<p>
  
```bash
aws ec2 monitor-instances --instance-ids <instance-id>
```

</p>
</details>

### Disable CloudWatch detailed monitoring

---

<details>
<summary>Show commands / answers</summary>
<p>
  
```bash
aws ec2 unmonitor-instances --instance-ids <instance-id>
```

</p>
</details>

# Miscellaneous

### Create snapshot of a volume

---

<details>
<summary>Show commands / answers</summary>
<p>
  
```bash
aws ec2 create-snapshot --volume-id <volume-id> --description "My snapshot"
```

</p>
</details>

### List all snapshots

---

<details>
<summary>Show commands / answers</summary>
<p>
  
```bash
aws ec2 describe-snapshots --owner-ids self
```

</p>
</details>

