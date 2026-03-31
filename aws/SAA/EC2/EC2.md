# EC2 Purchasing Options — What Problem They Solve

---

## On-Demand
> *"I have no idea what's going to happen"*

**When to use**
- Unpredictable traffic or sudden spikes
- Short-term testing, demos, dev environments
- First time running an app (before committing to anything)

**Exam scenarios**
- Startup that just launched and doesn't know its load profile
- QA environment that spins up for a few hours

**Don't use when** you have predictable workloads — you're paying the highest rate for no reason.

Pros: Maximum flexibility  
Cons: Most expensive per hour

---

## Reserved Instances (RI)
> *"I know exactly what I'll use for 1 or 3 years"*

**When to use**
- Production databases (MySQL, PostgreSQL, Oracle)
- Always-on application servers
- Any stable, predictable workload

**Key variants (the exam distinguishes these)**

| Type | Flexibility | Discount |
|---|---|---|
| Standard RI | Can only change AZ or size (same family) | Higher (~72%) |
| Convertible RI | Can change family, OS, tenancy | Lower (~54%) |

**Exam scenarios**
- RDS instance running 24/7 → Standard RI
- Workload that might migrate from `m5` to `c5` in the future → Convertible RI

**Exam tip:** Standard RIs can be sold on the *Reserved Instance Marketplace* if you no longer need them.

Pros: Big discount with commitment  
Cons: Low flexibility; you're billed even if you don't use the instance

---

## Savings Plans
> *"I know how much compute I'll consume, but not exactly how"*

**When to use**
- You use Lambda + EC2 + Fargate in combination
- You change instance types frequently
- You want a discount without locking into a specific instance

**Key variants**

| Type | Scope | Covers |
|---|---|---|
| Compute SP | Any region, family, OS | EC2, Lambda, Fargate |
| EC2 Instance SP | Fixed family + region | EC2 only, higher discount |

**Exam scenarios**
- Company with microservices on Lambda and containers on ECS Fargate → Compute Savings Plan
- Only ever running `m5` in `us-east-1` → EC2 Instance SP (cheaper)

Pros: Discount + configuration flexibility  
Cons: Committed to a $/hour spend for 1 or 3 years

---

## Spot Instances
> *"I want the cheapest compute and can tolerate interruptions"*

**When to use**
- Batch jobs, image/video processing
- Machine learning training
- Big data (EMR), distributed analytics
- Workloads you can pause and resume

**When NOT to use (critical for the exam)**
- Databases
- Applications that can't be interrupted (production e-commerce)
- Anything stateful that you can't checkpoint

**Key mechanics**
- AWS gives you a **2-minute warning** before terminating your instance
- If the spot price exceeds your bid → instance is terminated
- **Spot Blocks** (fixed duration 1–6h): the exam mentions them but they are being discontinued

**Exam scenarios**
- Rendering movie frames → Spot (if a node fails, just re-render)
- Training an ML model overnight → Spot + checkpoint state to S3

Pros: Up to 90% cheaper than On-Demand  
Cons: Can be terminated at any time

---

## Dedicated Hosts
> *"I have licenses tied to physical hardware"*

**When to use**
- Software with **per-socket** or **per-core** licenses (Oracle DB, Windows Server, SQL Server)
- Compliance requiring knowledge of the exact physical server
- Regulations that prohibit hardware-level multi-tenancy

**Key difference vs Dedicated Instances**
- Dedicated Host: **you control VM placement** on the physical server. You can see the number of sockets/cores.
- Dedicated Instance: AWS manages the hardware; it only guarantees no sharing with other accounts.

**Exam scenarios**
- Bank with audits requiring a documented dedicated server → Dedicated Host
- Existing Oracle DB with a per-socket license → Dedicated Host (BYOL)

Pros: Full physical server control, BYOL support  
Cons: Most expensive; you reserve the entire host even if not fully utilized

---

## Dedicated Instances
> *"I need hardware isolation, but don't care about the exact server"*

**When to use**
- Internal security policies that prohibit shared hardware
- Moderate compliance requirements (not at the Dedicated Host level)

**What it guarantees:** your instance does not share hardware with instances from **other AWS accounts**.  
**What it does NOT guarantee:** you may still share the same host with other instances from **your own account**.

**Exam scenarios**
- Company with a no-shared-hardware-with-third-parties policy but no per-socket licenses → Dedicated Instance

Pros: Hardware isolation without managing the host  
Cons: More expensive than On-Demand; less control than Dedicated Host

---

## Capacity Reservations
> *"I MUST have capacity available — cost is secondary"*

**When to use**
- Disaster recovery: you must be able to launch instances in a specific AZ under any circumstance
- Critical time-bound events (product launches, Black Friday)
- Regulated workloads requiring guaranteed availability

**Details the exam exploits**
- No discount on their own — **combine with RI or Savings Plans** to get a discount
- You're billed even if you never launch an instance
- Scoped **per AZ**, not per region — you must specify the AZ

**Exam scenarios**
- Need to guarantee you can launch 50 `c5.4xlarge` instances in `us-east-1a` during an event → Capacity Reservation + RI for the discount

Pros: Guaranteed capacity in a specific AZ  
Cons: No built-in discount; you pay regardless of whether you use the capacity

---

## Quick Decision Table

| Situation | Option |
|---|---|
| No idea how much I'll use | On-Demand |
| Stable workload, same instance for years | Standard RI |
| Stable workload, might change instance family | Convertible RI or Savings Plan |
| Mix of Lambda + EC2 + Fargate | Compute Savings Plan |
| Fault-tolerant batch jobs | Spot |
| Oracle/Windows per-socket licenses | Dedicated Host |
| Hardware isolation without BYOL | Dedicated Instance |
| Capacity guarantee for DR or events | Capacity Reservation |
| Want discount AND capacity guarantee | Capacity Reservation + RI |

---

# EC2 Spot — Requests, Fleets & Blocks

> Spot gives you up to 90% savings in exchange for one thing: AWS can take the instance back.
> Understanding *how* that works — and how to defend against it — is what this section is about.

---

## Spot Instance Requests
> *"I want cheap compute and I'll set my price ceiling"*

You bid a **max price**. The instance runs as long as `spot_price < max_price`.
Spot price fluctuates based on supply and demand per AZ and instance type.

### Interruption behavior
When `spot_price > max_price`, AWS can:
- **Stop** the instance (if EBS-backed — state is preserved)
- **Terminate** the instance (data on instance store is lost)
- **Hibernate** the instance (RAM saved to EBS)

You always get a **2-minute warning** via the instance metadata endpoint:
```
http://169.254.169.254/latest/meta-data/spot/interruption-notice
```

### Request types (the exam distinguishes these)

| Type | Behavior |
|---|---|
| One-time | Request fulfilled once; if interrupted, it's gone |
| Persistent | AWS re-submits the request after interruption until valid or expired |

**Exam trap:** to fully cancel a persistent Spot request, you must **cancel the request first**, then terminate the instance. If you only terminate, AWS re-launches it.

### Use cases
- Batch jobs, ETL pipelines
- Image/video processing
- ML training with checkpointing to S3
- Any stateless, fault-tolerant workload

### Not suitable for
- Databases
- Critical or stateful systems
- Anything that can't handle abrupt termination

---

## Spot Fleet
> *"I want Spot at scale — across multiple instance types and AZs"*

A Spot Fleet is a **collection of Spot Instances (+ optional On-Demand)** that together meet a target capacity.

Instead of betting on one instance type, you define **multiple pools** and let AWS fulfill the capacity from whichever combination works best.

### What you define
- Target capacity (units: instances, vCPUs, or memory)
- One or more **launch templates** with different instance types, AZs, and OS configs
- Optional On-Demand base capacity (for the critical portion of your workload)
- Max total cost

AWS stops launching instances when target capacity or max cost is reached.

### Allocation strategies

| Strategy | How it works | Best for |
|---|---|---|
| `lowestPrice` | Picks the cheapest pool | Cost-first, short jobs |
| `diversified` | Spreads across all pools | Long-running, high availability |
| `capacityOptimized` | Picks pools with most available capacity | Reducing interruptions |
| `priceCapacityOptimized` ✅ | Balances lowest price + available capacity | **Recommended default** |

**Exam tip:** `priceCapacityOptimized` is the answer when the question asks for *both* cost savings *and* availability. `lowestPrice` is cheaper but gets interrupted more. `diversified` is the answer when the question emphasizes availability over cost.

### Use cases
- Large-scale batch processing (hundreds of instances)
- Big data clusters (EMR)
- CI/CD farms that need to scale quickly
- ML training distributed across many nodes

---

## Spot Blocks (Legacy ⚠️)
> *"I need Spot capacity for a fixed window without interruption"*

Spot Blocks let you reserve a Spot Instance for a **defined duration of 1 to 6 hours** with no interruption during that window — in most cases.

AWS has deprecated this feature for new customers. It still appears on the exam as a concept.

### How it differs from a regular Spot request

| | Spot Request | Spot Block |
|---|---|---|
| Duration | Indefinite (until interrupted) | Fixed: 1–6 hours |
| Interruption risk | Yes, anytime | Rare, only in extreme capacity constraints |
| Price | Fluctuates | Fixed for the duration |
| Status | Active | Being phased out |

### Historical use cases
- Short batch jobs with a hard deadline
- Time-bound processing that couldn't afford mid-job interruption
- Jobs too short for Reserved Instances but too critical for regular Spot

**Exam approach:** if a question mentions Spot Blocks, it's testing whether you know they existed and what they solved — not whether you'd architect with them today. Prefer Spot Fleet with `capacityOptimized` for modern fault-tolerant designs.

---

# EC2 Placement Groups & Elastic Network Interfaces

> Two features that give you explicit control over *where* your instances live (Placement Groups)
> and *how* they connect to the network (ENI).

---

# Placement Groups
> *"I want to control how AWS physically places my instances on hardware"*

By default, AWS places instances wherever capacity is available.
Placement Groups let you override that — either pulling instances **closer together** or **pushing them apart** — depending on what your workload needs.

---

## Cluster
> *"I need the lowest possible latency between instances"*

All instances are packed onto hardware within the **same rack, same AZ**.
This gives you the fastest possible network between them: up to **10 Gbps** bandwidth with Enhanced Networking.

**The tradeoff:** if the rack fails, all instances fail together.

### How it works
```
[ Rack A          ]
  [i-1] [i-2] [i-3]   ← all on the same physical rack
```

### Use cases
- **HPC (High Performance Computing):** tightly coupled jobs where nodes constantly exchange data (MPI workloads)
- **Financial trading systems:** microsecond-level latency between services
- **In-memory distributed computing:** Spark jobs where shuffle speed is the bottleneck
- **Video encoding pipelines:** large files transferred between nodes at high throughput

### Exam signals
- Question mentions "lowest latency", "high throughput between instances", or "tightly coupled" → Cluster
- Question mentions "same AZ" as a constraint → likely Cluster
- ⚠️ If the question also mentions "fault tolerance" → Cluster is the wrong answer

Pros: Best network performance between instances  
Cons: Single point of failure at the rack level; all instances must be in the same AZ

---

## Spread
> *"I have a small number of critical instances that must never fail together"*

Each instance is placed on **separate underlying hardware** — different racks, different power sources, different network paths.

**Hard limit: 7 instances per AZ per placement group.**
This limit is intentional — it forces you to keep the group small and truly critical.

### How it works
```
AZ us-east-1a
  Rack A → [i-1]
  Rack B → [i-2]
  Rack C → [i-3]   ← each on isolated hardware
```

### Use cases
- **Primary/secondary database pairs:** if one host fails, the standby is on different hardware
- **ZooKeeper or etcd quorum nodes:** losing two nodes at once would break consensus
- **Active-passive application tiers:** the failover instance must survive the same hardware failure that took down the primary
- **Small fleets of critical instances** where each one matters individually

### Exam signals
- Question mentions "maximum fault isolation", "individual instances", or a small count (≤7) of critical instances → Spread
- Question mentions "different hardware per instance" → Spread
- ⚠️ If you need more than 7 instances per AZ → Spread won't work, use Partition

Pros: Maximum fault isolation; each instance is on independent hardware  
Cons: Hard cap of 7 instances per AZ; not suitable for large-scale deployments

---

## Partition
> *"I have a large distributed system and I need fault isolation at the group level, not per instance"*

Instances are divided into **logical partitions** (up to 7 per AZ). Each partition runs on its own set of racks — isolated from other partitions. Instances within the same partition may share hardware.

You can have **hundreds of instances** across partitions. AWS exposes the partition number to your application via instance metadata, so your software can be partition-aware.

### How it works
```
AZ us-east-1a
  Partition 1 → [i-1] [i-2] [i-3]   ← shared rack, isolated from P2
  Partition 2 → [i-4] [i-5] [i-6]   ← separate rack
  Partition 3 → [i-7] [i-8] [i-9]   ← separate rack
```

### Use cases
- **Hadoop / HDFS:** replicas are placed in different partitions so a rack failure doesn't lose all copies of a block
- **Kafka brokers:** partition leaders and followers spread across physical racks for durability
- **Cassandra clusters:** replication strategy maps to physical partitions for rack-aware placement
- **HBase, Accumulo:** same pattern — large clusters that need rack-level fault isolation

### Exam signals
- Question mentions "large distributed systems", "rack-aware", "Hadoop", "Kafka", or "Cassandra" → Partition
- Question mentions "hundreds of instances" with fault isolation → Partition (not Spread)
- Question asks about accessing partition metadata → only Partition exposes this

Pros: Scales to hundreds of instances; rack-level fault isolation; partition-aware apps  
Cons: Instances within a partition still share hardware; not full per-instance isolation like Spread

---

## Quick Comparison

| | Cluster | Spread | Partition |
|---|---|---|---|
| Goal | Performance | Isolation per instance | Isolation per group |
| Max instances | No hard limit (same AZ) | 7 per AZ | Hundreds, up to 7 partitions/AZ |
| Failure domain | Entire group (same rack) | Independent per instance | Per partition (rack) |
| Typical workload | HPC, low-latency | Small critical systems | Large distributed systems |
| Multi-AZ support | ❌ | ✅ | ✅ |

---

# Elastic Network Interface (ENI)
> *"A virtual network card you can attach, detach, and move between EC2 instances"*

Every EC2 instance has at least one ENI. It's the object that holds all network configuration — the instance itself is just compute. The networking lives in the ENI.

---

## What an ENI contains

| Attribute | Notes |
|---|---|
| Primary private IPv4 | Fixed for the ENI's lifetime |
| One or more secondary private IPs | Useful for hosting multiple services on one instance |
| Public IPv4 (optional) | Assigned from AWS pool; changes on stop/start unless Elastic IP |
| Elastic IP (optional) | Static public IP, attached per private IP |
| IPv6 address (optional) | |
| Security Groups | Attached at the ENI level, not the instance level |
| MAC address | Fixed to the ENI — survives instance replacement |
| Source/destination check flag | Disable this on NAT instances and routers |

---

## Key behaviors

**ENIs are independent objects.** You can:
- Create an ENI without an instance
- Attach it to an instance
- Detach it and re-attach it to a different instance — with all its IPs, security groups, and MAC address intact

**This is the core exam mechanic:** moving an ENI between instances moves its entire network identity.

**ENIs are AZ-scoped.** An ENI created in `us-east-1a` can only attach to instances in `us-east-1a`.

---

## Use cases

### Failover with IP preservation
Your primary instance fails. You detach its ENI and attach it to a standby instance.
The standby now has the same private IP, same Elastic IP, same MAC address — no DNS changes, no client reconfiguration needed.

**Exam scenario:** "How do you fail over an EC2 instance while preserving its IP address?" → Move the ENI.

### License-locked software (MAC-based licensing)
Some software licenses are tied to the MAC address of the NIC.
Since the MAC address lives on the ENI — not the instance — you can replace the underlying EC2 instance without invalidating the license. Just move the ENI.

**Exam scenario:** "A software license is bound to a MAC address. The instance needs to be replaced. How?" → Detach ENI, attach to new instance.

### Multiple network interfaces on one instance
Attach a second ENI to an instance to connect it to two subnets simultaneously.
Common for:
- **Network appliances** (firewalls, NAT, proxies) that need one interface on a public subnet and one on a private subnet
- **Dual-homed instances** that serve traffic on two separate VPCs or security zones

### Management network separation
Attach a dedicated ENI on a management subnet (with its own security group, stricter rules) for SSH/admin access, separate from the ENI handling application traffic.

---

## Exam signals

- "Preserve IP after instance replacement" → move the ENI
- "MAC-based software license on EC2" → move the ENI
- "Instance needs interfaces in two subnets" → attach a second ENI
- "Security group applied at instance level" → technically wrong; security groups attach to ENIs
- "Source/destination check" → disable on NAT instances; this flag lives on the ENI

Pros: Portable network identity; enables failover, multi-homing, and license portability  
Cons: AZ-scoped; can't move an ENI across AZs or regions

# EC2 Volumes

# AWS EC2 Storage Overview

## 1. EBS (Elastic Block Store)
**Definition:** Network-attached persistent storage for EC2 instances.

**Key Features:**
- Persistent even after instance termination (unless root volume with `Delete on Termination`).  
- Can only be attached to **one instance at a time** (except io1/io2 Multi-Attach).  
- Bound to an **Availability Zone (AZ)**; moving requires a **snapshot**.  
- Provisioned capacity: Size (GB) and IOPS; billed based on provisioned capacity.  
- Snapshots can be copied across AZs or regions.  
- Volume types:  
  - **gp2 / gp3 (SSD):** General purpose, balanced price-performance.  
  - **io1 / io2 / io2 Block Express:** High-performance SSD, low latency, PIOPS.  
  - **st1 (HDD):** Throughput-optimized, low cost.  
  - **sc1 (HDD):** Cold storage, infrequently accessed, cheapest.  
- **Multi-Attach:** io1/io2 volumes can attach to **up to 16 instances** in the same AZ (requires cluster-aware FS).  
- **Encryption:** At rest, in transit, and snapshots encrypted via KMS (AES-256), transparent to user.  

**Use Cases:**
- Root or boot volumes for EC2 instances.  
- Databases requiring consistent IOPS (gp3/io2).  
- Persistent storage for logs or critical data with snapshot backups.  
- Multi-Attach for clustered Linux applications (e.g., Teradata).  

---

## 2. EC2 Instance Store
**Definition:** Local physical storage on the EC2 host.

**Key Features:**
- Very high IOPS and low latency.  
- **Ephemeral:** Lost if the instance stops or the host fails.  
- Best for **temporary data**: buffers, caches, scratch data.  

**Use Cases:**
- High-performance cache.  
- Temporary or intermediate storage for data processing.  
- Scratch space for streaming or big data workloads.  

---

## 3. AMI (Amazon Machine Image)
**Definition:** Preconfigured EC2 image with OS, software, and settings.

**Types:** Public, private, or AWS Marketplace AMIs.  

**Process:** Stop instance → create AMI → includes EBS snapshots → launch new instances from AMI.  

**Use Cases:**
- Rapid deployment of identical EC2 instances.  
- Backup of instance configuration.  
- Reproducible dev/test environments.  

---

## 4. EBS Snapshots
**Definition:** Point-in-time backup of EBS volumes.

**Features:**
- Can copy across AZs or regions.  
- **Snapshot Archive:** 75% cheaper, restore takes 24–72 hours.  
- **Recycle Bin:** Recover deleted snapshots, configurable retention.  
- **Fast Snapshot Restore (FSR):** Initialize snapshot fully to remove first-use latency (extra cost).  

**Use Cases:**
- Volume backups.  
- Migration between AZs or regions.  
- Fast recovery in case of volume failure.  

---

## 5. EFS (Elastic File System)
**Definition:** Network file system, multi-AZ, POSIX compliant, Linux only.

**Key Features:**
- Scales automatically; pay-per-use.  
- Protocol: NFSv4.1, concurrent access for thousands of clients.  
- **Performance Modes:**  
  - **General Purpose:** Low-latency, web servers, CMS.  
  - **Max I/O:** High throughput, parallel workloads.  
- **Throughput Modes:**  
  - Bursting (size-based)  
  - Provisioned (fixed throughput)  
- **Storage Classes:** Standard, Infrequent Access (EFS-IA), Archive.  

**Use Cases:**
- Shared content across multiple EC2 instances (e.g., WordPress).  
- Highly concurrent and scalable workloads.  
- POSIX-compliant applications needing multi-AZ access.  

---

## 6. Quick Comparison

| Feature                  | EBS                      | Instance Store         | EFS                          |
|--------------------------|-------------------------|----------------------|-----------------------------|
| Persistence               | Yes                     | No                   | Yes                          |
| Multi-AZ                  | No (only snapshot copy) | No                   | Yes                          |
| Multi-instance Mount      | No (except io2 Multi-Attach) | No               | Yes                          |
| Performance               | Medium-High             | Very High            | Variable (GP vs Max I/O)    |
| Typical Use Case          | Boot volumes, DB, apps  | Cache/temp           | Shared files, multi-EC2     |
