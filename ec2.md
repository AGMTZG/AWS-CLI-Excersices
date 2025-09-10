# EC2

# Basic operations with EC2

### List all instances

---

<details>
<summary>Show commands / answers</summary>
<p>

```bash
aws ec2 describe-instances
```

</p>
</details>

### Create a new EC2 instance

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

### Start the instance

---

<details>
<summary>Show commands / answers</summary>
<p>
  
```bash
aws ec2 start-instances --instance-ids <instance-id>
```

</p>
</details>

### Stop the instance

---

<details>
<summary>Show commands / answers</summary>
<p>
  
```bash
aws ec2 stop-instances --instance-ids <instance-id>
```

</p>
</details>

### Reboot the instance

---

<details>
<summary>Show commands / answers</summary>
<p>
  
```bash
aws ec2 reboot-instances --instance-ids <instance-id>
```

</p>
</details>

### Terminate the instance

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

### Create a new key pair

---

<details>
<summary>Show commands / answers</summary>
<p>
  
```bash
aws ec2 create-key-pair --key-name MyKey --query 'KeyMaterial' --output text > MyKey.pem
chmod 400 MyKey.pem
```

</p>
</details>

### List all key pairs

---

<details>
<summary>Show commands / answers</summary>
<p>
  
```bash
aws ec2 describe-key-pairs
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

### Get public DNS of an instance

---

<details>
<summary>Show commands / answers</summary>
<p>
  
```bash
aws ec2 describe-instances --instance-ids <instance-id> --query 'Reservations[*].Instances[*].PublicDnsName' --output text
```

</p>
</details>

### Get private IP of an instance

---

<details>
<summary>Show commands / answers</summary>
<p>
  
```bash
aws ec2 describe-instances --instance-ids <instance-id> --query 'Reservations[*].Instances[*].PrivateIpAddress' --output text
```

</p>
</details>

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

