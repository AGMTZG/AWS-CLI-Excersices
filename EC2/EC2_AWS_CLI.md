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
  --instance-type t3.micro \
  --key-name <KeyPairName> \
  --security-group-ids <sg-xxxx> \
  --subnet-id <subnet-xxxx>
```

</p>
</details>

### List all Free Tier Instances Types

---

<details>
<summary>Show commands / answers</summary>
<p>

```bash
aws ec2 describe-instance-types --query "InstanceTypes[?FreeTierEligible==\`true\`].InstanceType" --output text
```

</p>
</details>

### Launch a new EC2 spot instance 

---

<details>
<summary>Show commands / answers</summary>
<p>

```bash
aws ec2 run-instances \
  --image-id <AMI_ID> \
  --count 1 \
  --instance-type t3.micro \
  --instance-market-options 'MarketType=spot' \
  --key-name <KeyPairName> \
  --security-group-ids <sg-xxxx> \
  --subnet-id <subnet-xxxx>
```

</p>
</details>

### Start a stopped EC2 instance

---

<details>
<summary>Show commands / answers</summary>
<p>
  
```bash
aws ec2 start-instances --instance-ids <instance-id>
```

</p>
</details>

### Stop a running EC2 instance

---

<details>
<summary>Show commands / answers</summary>
<p>
  
```bash
aws ec2 stop-instances --instance-ids <instance-id>
```

</p>
</details>

### Reboot a running EC2 instance

---

<details>
<summary>Show commands / answers</summary>
<p>
  
```bash
aws ec2 reboot-instances --instance-ids <instance-id>
```

</p>
</details>

### Terminate an EC2 instance

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

### List all key pairs in your AWS account

---

<details>
<summary>Show commands / answers</summary>
<p>
  
```bash
aws ec2 describe-key-pairs
```

</p>
</details>

### Delete a key pair

---

<details>
<summary>Show commands / answers</summary>
<p>
  
```bash
aws ec2 delete-key-pair --key-name <your key>
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
aws ec2 describe-vpcs --query "Vpcs[].VpcId" --output text
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

### List all security groups with details such as Name, Description and ID

---

<details>
<summary>Show commands / answers</summary>
<p>

```bash
aws ec2 describe-security-groups --query 'SecurityGroups[].{Name:GroupName,Description:Description,ID:GroupId}' --output table
```

</p>
</details>


### Retrieve the public IP address of a specific EC2 instance

---

<details>
<summary>Show commands / answers</summary>
<p>
  
```bash
aws ec2 describe-instances --instance-ids <your instance id> --query "Reservations[].Instances[].PublicIpAddress" --output text
```

</p>
</details>

### Retrieve the private IP address of a specific EC2 instance

---

<details>
<summary>Show commands / answers</summary>
<p>
  
```bash
aws ec2 describe-instances --instance-ids <your instance id> --query "Reservations[].Instances[].PrivateIpAddress" --output text
```

</p>
</details>

### Retrieve the public DNS of a specific EC2 instance

---

<details>
<summary>Show commands / answers</summary>
<p>
  
```bash
aws ec2 describe-instances --instance-ids <your instance id> --query "Reservations[].Instances[].PublicDnsName" --output text
```

</p>
</details>

# Security Groups

### List all Security Groups in your AWS account

---

<details>
<summary>Show commands / answers</summary>
<p>
  
```bash
aws ec2 describe-security-groups
```

</p>
</details>

### Create a new Security Group in your AWS account

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

### Add an inbound rule to a Security Group to allow specific types of network traffic to reach your resources

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

### Add an outbound rule to a Security Group to allow specific types of network traffic to leave your resources

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

### Allocate a new Elastic IP address in your AWS account to assign a static public IP to an instance or resource

---

<details>
<summary>Show commands / answers</summary>
<p>
  
```bash
aws ec2 allocate-address
```

</p>
</details>

### Associate an Elastic IP address with a specific EC2 instance to give it a static public IP

---

<details>
<summary>Show commands / answers</summary>
<p>
  
```bash
aws ec2 associate-address --instance-id <instance-id> --allocation-id <allocation-id>
```

</p>
</details>

### Release an Elastic IP address to remove it from your AWS account and make it available for other users

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

### List all EBS volumes in your AWS account

---

<details>
<summary>Show commands / answers</summary>
<p>
  
```bash
aws ec2 describe-volumes
```

</p>
</details>

### Create a new EBS volume

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

### Attach an EBS volume to a specific EC2 instance

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

### Detach an EBS volume from a specific EC2 instance

---

<details>
<summary>Show commands / answers</summary>
<p>
  
```bash
aws ec2 detach-volume --volume-id <volume-id>
```

</p>
</details>

### Delete an EBS volume to permanently remove it from your AWS account

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

### List all available Amazon Machine Images (AMIs) in your AWS account or region

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

### List all tags associated with your EC2 instances

---

<details>
<summary>Show commands / answers</summary>
<p>
  
```bash
aws ec2 describe-tags --filters "Name=resource-id,Values=<instance-id>"
```

</p>
</details>

### Add a tag to an EC2 instance

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

### Get the current status of an EC2 instance

---

<details>
<summary>Show commands / answers</summary>
<p>
  
```bash
aws ec2 describe-instance-status --instance-ids <instance-id>
```

</p>
</details>

# Snapshots

### Create a snapshot of an EBS volume to back up its current state and data

---

<details>
<summary>Show commands / answers</summary>
<p>
  
```bash
aws ec2 create-snapshot --volume-id <volume-id> --description "My snapshot"
```

</p>
</details>

### List all EBS snapshots in your AWS account

---

<details>
<summary>Show commands / answers</summary>
<p>
  
```bash
aws ec2 describe-snapshots --owner-ids self
```

</p>
</details>

### Copy snapshot from ec2 instance to another region

---

<details>
<summary>Show commands / answers</summary>
<p>
  
```bash
aws ec2 copy-snapshot \
  --source-region <source-region> \
  --source-snapshot-id <snapshot id> \
  --region <region> \
  --description <your description>
```

</p>
</details>

# AWS Load Balancers CLI Cheatsheet

---

## 1. Classic Load Balancer (CLB)

### Create a Classic Load Balancer using HTTP on port 80, forwarding requests to instances on port 80 over HTTP.

---

<details>
<summary>Show commands / answers</summary>
<p>
  
```bash
aws elb create-load-balancer \
  --load-balancer-name <your load balancer name> \
  --listeners "Protocol=HTTP,LoadBalancerPort=80,InstanceProtocol=HTTP,InstancePort=80" \
  --subnets subnet-xxxx subnet-yyyy \
  --security-groups sg-xxxx
```

</p>
</details>


### Register instances with the load balancer you just created.

---

<details>
<summary>Show commands / answers</summary>
<p>
  
```bash
aws elb register-instances-with-load-balancer \
  --load-balancer-name <your load balancer name> \
  --instances <your instance ids>
```

</p>
</details>

### Configure health check for your load balancer

---

<details>
<summary>Show commands / answers</summary>
<p>
  
```bash
aws elb configure-health-check \
  --load-balancer-name my-clb \
  --health-check Target=HTTP:80/,Interval=30,Timeout=5,UnhealthyThreshold=2,HealthyThreshold=5

Hace requests a:
http://<instance-ip>:80/
```

</p>
</details>

### List all classic load balancers

---

<details>
<summary>Show commands / answers</summary>
<p>
  
```bash
aws elb describe-load-balancers
```

</p>
</details>

### Delete a classic load balancer

---

<details>
<summary>Show commands / answers</summary>
<p>
  
```bash
aws elb delete-load-balancer \
  --load-balancer-name <your load balancer name>
```

</p>
</details>

## Applicacion load balancers

### Create an application load balancer

---

<details>
<summary>Show commands / answers</summary>
<p>
  
```bash
aws elbv2 create-load-balancer \
  --name <your load balancer name> \
  --subnets subnet-xxx subnet-xxx \
  --security-groups sg-xxx \
  --scheme internet-facing
```

</p>
</details>

### Create Target Group for the load balancer you just created

---

<details>
<summary>Show commands / answers</summary>
<p>
  
```bash
aws elbv2 create-target-group \
  --name <your target application name> \
  --protocol HTTP \
  --port 80 \
  --vpc-id vpc-xxx
```

</p>
</details>

### Register the instances in the target group

---

<details>
<summary>Show commands / answers</summary>
<p>
  
```bash
aws elbv2 register-targets \
  --target-group-arn <tg-arn> \
  --targets Id=i-xxx Id=i-xxx Id=i-xxx
```

</p>
</details>

### Create a command that defines how your incoming traffic will enter + port

---

<details>
<summary>Show commands / answers</summary>
<p>
  
```bash
aws elbv2 create-listener \
  --load-balancer-arn <lb-arn> \
  --protocol HTTP \
  --port 80 \
  --default-actions Type=forward,TargetGroupArn=<tg-arn>
```

</p>
</details>

### List all your new generation of load balancers

---

<details>
<summary>Show commands / answers</summary>
<p>
  
```bash
aws elbv2 describe-load-balancers
```

</p>
</details>

### List all your target groups

---

<details>
<summary>Show commands / answers</summary>
<p>
  
```bash
aws elbv2 describe-target-groups
```

</p>
</details>

## Network load balancers

### Create a network load balancer

---

<details>
<summary>Show commands / answers</summary>
<p>
  
```bash
aws elbv2 create-load-balancer \
  --name <your load balancer name> \
  --type network \
  --subnets subnet-123 subnet-456
```

</p>
</details>


### Create a tcp target group

---

<details>
<summary>Show commands / answers</summary>
<p>
  
```bash
aws elbv2 create-target-group \
  --name <your target group name> \
  --protocol TCP \
  --port 80 \
  --vpc-id vpc-xxx
```

</p>
</details>


### Create a Listener for the target group you just created

---

<details>
<summary>Show commands / answers</summary>
<p>
  
```bash
aws elbv2 create-listener \
  --load-balancer-arn <lb-arn> \
  --protocol TCP \
  --port 80 \
  --default-actions Type=forward,TargetGroupArn=<tg-arn>
```

</p>
</details>

## Gateway load balancer

### Create a gateway load balancer

---

<details>
<summary>Show commands / answers</summary>
<p>
  
```bash
aws elbv2 create-load-balancer \
  --name <your load balancer name> \
  --type gateway \
  --subnets subnet-xxx subnet-xxx
```

</p>
</details>

### Create Target Group (GENEVE)

---

<details>
<summary>Show commands / answers</summary>
<p>
  
```bash
aws elbv2 create-target-group \
  --name <your target group name> \
  --protocol GENEVE \
  --port 6081 \
  --vpc-id vpc-xxx
```

</p>
</details>


### Register Targets to the target group you just created
---

<details>
<summary>Show commands / answers</summary>
<p>
  
```bash
aws elbv2 register-targets \
  --target-group-arn <tg-arn> \
  --targets Id=i-xxx
```

</p>
</details>
