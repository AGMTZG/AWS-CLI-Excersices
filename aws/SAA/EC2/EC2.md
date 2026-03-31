# EC2 — Elastic Compute Cloud

> AWS's virtual machine service. You rent compute capacity in the cloud instead of buying physical servers.

---

## What it is

An EC2 instance is a **virtual server** running on AWS hardware.
You choose the OS, CPU, RAM, storage, and network — AWS manages the physical infrastructure underneath.

## What you control

- **Instance type** — defines CPU, RAM, and network bandwidth (e.g., `t3.micro`, `m5.xlarge`, `c6i.2xlarge`)
- **AMI** — the OS and software image the instance boots from
- **Storage** — EBS volumes, instance store, or EFS mounts
- **Network** — which VPC, subnet, and security groups apply
- **Purchasing model** — On-Demand, Reserved, Spot, etc.

## Instance type families — what the letters mean

| Family | Optimized for | Examples |
|---|---|---|
| `t` | Burstable general purpose | `t3.micro`, `t4g.small` |
| `m` | Balanced general purpose | `m5.large`, `m6i.xlarge` |
| `c` | Compute (CPU-heavy) | `c6i.2xlarge`, `c7g.large` |
| `r` | Memory (RAM-heavy) | `r6i.4xlarge`, `r7g.xlarge` |
| `i` | Storage (NVMe instance store) | `i3.large`, `i4i.xlarge` |
| `p` / `g` | GPU (ML training, graphics) | `p4d.24xlarge`, `g5.xlarge` |

## Exam signals

- "Resize an instance" → stop → change instance type → start (requires EBS root volume)
- "High CPU workload" → `c` family
- "In-memory database (Redis, SAP HANA)" → `r` family
- "Burstable low-cost dev instance" → `t` family
- "EC2 is unresponsive" → check security groups, then NACL, then route table

Pros: Full control over compute; integrates with every AWS service  
Cons: You manage the OS, patching, and scaling — unlike managed services (RDS, Lambda)

---

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

# EC2 Storage — EBS, Instance Store, AMI, Snapshots & EFS

> AWS gives you multiple storage primitives for EC2. The exam tests whether you can
> match the right one to the right workload — persistence, performance, and scope are the three axes.

---

# EBS — Elastic Block Store
> *"I need persistent, reliable storage attached to my EC2 instance"*

EBS is a **network-attached drive**. It lives independently from the instance — if the instance is terminated, the volume survives (unless it's a root volume with Delete on Termination enabled).

### Key mechanics

- **AZ-scoped:** an EBS volume in `us-east-1a` can only attach to instances in `us-east-1a`. To move it, take a snapshot and restore in another AZ.
- **One instance at a time** — with one exception: `io1/io2` Multi-Attach (up to 16 instances in the same AZ, requires a cluster-aware filesystem).
- **Billed on provisioned capacity** — you pay for what you allocate, not what you use.
- **Encryption** is transparent: at-rest, in-transit, and snapshots are all encrypted via KMS (AES-256). No application changes needed.

### Volume types — when to use each

| Type | Media | Best for | Exam signal |
|---|---|---|---|
| `gp3` | SSD | General purpose; baseline 3,000 IOPS, scalable to 16,000 | Default choice for most workloads |
| `gp2` | SSD | Legacy general purpose; IOPS scales with size | Older questions; prefer gp3 |
| `io2 / io2 Block Express` | SSD | Databases needing >16,000 IOPS or sub-ms latency | "High IOPS", "critical DB", "consistent latency" |
| `io1` | SSD | High IOPS, older generation | Same as io2 but io2 is better |
| `st1` | HDD | Sequential throughput (logs, Kafka, data warehouses) | "Throughput-optimized", "sequential", "big data" |
| `sc1` | HDD | Cold, infrequently accessed data | "Cheapest", "archival", "rarely accessed" |

**Exam trap:** `st1` and `sc1` **cannot be used as boot volumes**. Only SSD types (`gp2`, `gp3`, `io1`, `io2`) can be root volumes.

### Multi-Attach
`io1` and `io2` volumes can attach to **up to 16 instances simultaneously** within the same AZ.
The filesystem must be cluster-aware (not ext4 or xfs — those don't handle concurrent writes safely).

### Use cases

- **Root/boot volumes** for any EC2 instance (gp3 default)
- **Relational databases** (RDS on EC2, MySQL, PostgreSQL) needing consistent IOPS → `io2`
- **Log aggregation and streaming** with high sequential throughput → `st1`
- **Infrequently accessed backups or compliance archives** → `sc1`
- **Clustered Linux applications** (e.g., Teradata) requiring shared block storage → `io2` Multi-Attach

### Exam signals
- "Persistent storage that survives instance termination" → EBS
- "Move a volume to another AZ" → snapshot → restore in new AZ
- "Highest IOPS, lowest latency, sub-millisecond" → `io2 Block Express`
- "Cheapest block storage" → `sc1`
- "Sequential large reads/writes" → `st1`
- "Can't use as boot volume" → `st1` or `sc1`

Pros: Persistent, snapshotable, encrypted, flexible volume types  
Cons: AZ-scoped; single-instance by default; network latency vs instance store

---

# EC2 Instance Store
> *"I need the absolute fastest local storage and I don't care if it disappears"*

Instance Store is **physical storage on the host machine** running your EC2 instance.
There is no network hop — the disk is literally inside the same server.

### Key mechanics

- **Ephemeral by design:** data is lost when the instance stops, hibernates, or the underlying host fails. It survives a **reboot**.
- **Cannot be detached or snapshotted** — it's not a separate volume, it's tied to the physical host.
- **Highest possible IOPS** — some instance types (e.g., `i3`) deliver millions of IOPS, far beyond any EBS volume.

### Use cases

- **High-performance cache layer:** Redis or Memcached where data is rebuilt on restart anyway
- **Scratch space for ML training:** intermediate tensors and checkpoints during a training job (final model saved to S3)
- **Temporary ETL staging:** read from S3, transform in-memory + instance store, write result back to S3
- **Buffer for streaming pipelines:** Kafka brokers or Kinesis consumers staging data before flushing to durable storage
- **Big data shuffle space:** Spark intermediate shuffle files during a job

### Exam signals
- "Highest IOPS possible" → Instance Store
- "Temporary, ephemeral, scratch" → Instance Store
- "Data lost on stop or host failure is acceptable" → Instance Store
- "Cannot stop the instance" (because you'd lose data) → Instance Store in use
- ⚠️ "Persistent cache" → wrong answer for Instance Store; use EBS

Pros: Highest throughput and IOPS; zero network latency; no additional cost  
Cons: Ephemeral — data lost on stop/termination/host failure; can't snapshot or detach

---

# AMI — Amazon Machine Image
> *"I want to pre-bake my entire instance configuration and launch copies of it"*

An AMI is a **template** for launching EC2 instances. It captures the OS, installed software, configuration, and the EBS volume layout at a point in time.

### How AMI creation works
1. **Stop the instance** (recommended for a consistent filesystem state)
2. **Create AMI** → AWS takes EBS snapshots of all attached volumes
3. The AMI references those snapshots
4. **Launch new instances** from the AMI — each gets its own EBS volumes restored from those snapshots

AMIs are **region-scoped** but can be copied to other regions.
AMIs can be **public**, **private**, or shared with specific AWS accounts.

### Use cases

- **Golden image deployments:** bake your application, dependencies, and config into an AMI; Auto Scaling launches pre-configured instances in seconds instead of running user-data scripts at boot
- **Immutable infrastructure:** instead of patching running instances, build a new AMI and replace the fleet
- **Multi-region deployments:** build once in `us-east-1`, copy AMI to `eu-west-1`, launch identical stacks
- **Disaster recovery:** maintain a current AMI of production instances so you can rebuild the environment in another region quickly
- **Marketplace products:** launch pre-configured third-party software (firewalls, databases, monitoring tools) from AWS Marketplace AMIs

### Exam signals
- "Pre-configure instances for faster Auto Scaling launch" → custom AMI (vs user-data scripts)
- "Copy an EC2 environment to another region" → copy AMI to target region
- "Reproducible, identical instances" → AMI
- "Backup of instance configuration" → AMI (not a snapshot — snapshots are volume-level)

Pros: Fast, consistent instance launches; region-portable; captures full environment  
Cons: Point-in-time only; stale AMIs need rebuilding; EBS snapshots incur storage cost

---

# EBS Snapshots
> *"I need a point-in-time backup of my volume that I can restore, copy, or archive"*

Snapshots are **incremental backups** of EBS volumes stored in S3 (managed by AWS — you don't see the S3 bucket directly). The first snapshot is a full copy; subsequent ones only store changed blocks.

### Features that matter for the exam

| Feature | What it does | When to use |
|---|---|---|
| Cross-AZ / cross-region copy | Copy a snapshot to restore a volume in another AZ or region | Migration, DR |
| Snapshot Archive | Move to archive tier — 75% cheaper, restore takes 24–72 hours | Long-term retention, compliance |
| Recycle Bin | Deleted snapshots go here with configurable retention (1 day – 1 year) | Accidental deletion protection |
| Fast Snapshot Restore (FSR) | Pre-warms the snapshot so the restored volume has full IOPS immediately | When first-use latency is unacceptable |

**Exam trap:** by default, a restored EBS volume from a snapshot has lazy loading — blocks are pulled from S3 on first access, causing high latency on initial reads. FSR eliminates this at extra cost. Alternatively, you can manually pre-warm by reading all blocks with `fio` or `dd`.

### Use cases

- **Volume migration between AZs:** snapshot → copy → restore in target AZ
- **Cross-region DR:** snapshot → copy to DR region → restore if needed
- **Pre-production environment cloning:** snapshot production DB volume → restore in dev/staging
- **Compliance archival:** move old snapshots to Snapshot Archive for cheap long-term retention
- **Rollback:** snapshot before a risky migration; restore if something goes wrong

### Exam signals
- "Move EBS volume to another AZ" → snapshot + restore
- "Reduce first-read latency on restored volume" → Fast Snapshot Restore
- "Cheapest way to retain snapshots long-term" → Snapshot Archive
- "Accidentally deleted a snapshot" → Recycle Bin

Pros: Incremental, cross-region portable, multiple tiers for cost optimization  
Cons: Restoring across AZ requires manual snapshot + restore workflow; FSR has extra cost

---

# EFS — Elastic File System
> *"Multiple EC2 instances across multiple AZs all need to read and write the same files"*

EFS is a **fully managed NFS file system** that multiple instances can mount simultaneously.
Unlike EBS (one instance, one AZ), EFS is **multi-AZ and multi-instance by design**.

### Key mechanics

- **Linux only** — uses NFSv4.1. Not supported on Windows instances.
- **POSIX-compliant:** standard file permissions, ownership, locking — drop-in for any Linux app that reads/writes files.
- **Scales automatically:** no capacity to provision; you pay per GB stored.
- **Thousands of concurrent clients** can mount the same filesystem.

### Performance modes

| Mode | Latency | Throughput | Use when |
|---|---|---|---|
| General Purpose (default) | Low | Moderate | Web servers, CMS, home directories |
| Max I/O | Higher | Very high | Big data, media processing, parallel workloads with hundreds of clients |

### Throughput modes

| Mode | How it works | Use when |
|---|---|---|
| Bursting | Throughput scales with storage size | Unpredictable, spiky workloads |
| Provisioned | Fixed throughput regardless of storage size | Need consistent throughput independent of how much data is stored |
| Elastic (newest) | Auto-scales throughput up and down | Unpredictable workloads; simplest option |

### Storage classes

| Class | Cost | Access pattern |
|---|---|---|
| Standard | Higher | Frequently accessed |
| Infrequent Access (EFS-IA) | ~92% cheaper | Files not accessed for days/weeks |
| Archive | Cheapest | Rarely accessed |

Use **lifecycle policies** to automatically move files between classes based on last-access time.

### Use cases

- **Shared web content:** multiple web server instances behind an ALB all serve the same static files from EFS (e.g., WordPress uploads)
- **Home directories at scale:** thousands of users, each with a home directory on the same EFS filesystem
- **CI/CD build artifacts:** Jenkins agents on different instances read/write shared build caches
- **Container shared storage:** ECS or EKS tasks across multiple nodes mounting the same EFS volume
- **Data science workloads:** multiple EC2 instances processing the same large dataset in parallel

### Exam signals
- "Shared storage across multiple EC2 instances" → EFS (not EBS)
- "Multi-AZ shared filesystem" → EFS
- "Linux POSIX-compliant shared storage" → EFS
- "Windows shared storage" → FSx for Windows File Server (not EFS)
- "Hadoop HDFS replacement" → EFS or FSx for Lustre (not EFS for high-performance HPC)
- "Automatically scales storage" → EFS (vs EBS where you provision capacity)

Pros: Multi-AZ, multi-instance, auto-scaling, POSIX-compliant  
Cons: Linux only; more expensive than EBS per GB; latency higher than EBS for single-instance workloads

---

# Quick Decision Table

| Situation | Answer |
|---|---|
| Boot volume for EC2 | EBS `gp3` |
| Highest IOPS database (sub-ms latency) | EBS `io2 Block Express` |
| Sequential log/data warehouse throughput | EBS `st1` |
| Coldest, cheapest block storage | EBS `sc1` |
| Temporary scratch space, maximum speed | EC2 Instance Store |
| Shared storage across multiple instances | EFS |
| Windows shared file system | FSx for Windows |
| Multi-AZ shared NFS | EFS |
| Point-in-time volume backup | EBS Snapshot |
| Move volume to another AZ | Snapshot → restore |
| Pre-configured instance template | AMI |
| Eliminate first-read latency on restore | Fast Snapshot Restore |
| Long-term cheap snapshot retention | Snapshot Archive |
