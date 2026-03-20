# 🧾 AWS EC2 Purchasing Options – Cheat Sheet

## 🟢 On-Demand
- **Pricing:** Pay per second (Linux/Windows) or hourly
- **Commitment:** None
- **Cost:** Highest
- **Use case:** Short-term, unpredictable workloads
- **Key idea:** Flexibility > cost

---

## 🔵 Reserved Instances (RI)
- **Commitment:** 1 or 3 years
- **Discount:** Up to ~72%
- **Scope:** Region or AZ (Zonal = capacity reservation)
- **Payment options:** No / Partial / All upfront
- **Use case:** Steady-state workloads (databases)

### Variants:
- **Standard RI:** Highest discount, least flexible  
- **Convertible RI:** Lower discount, can change instance type/OS  

---

## 🟣 Savings Plans
- **Commitment:** $/hour for 1 or 3 years
- **Discount:** Up to ~72%
- **Flexibility:** High (size, OS, tenancy)
- **Types:**
  - **Compute Savings Plan:** Most flexible
  - **EC2 Instance Savings Plan:** Limited to family + region
- **Use case:** Predictable usage with flexibility

---

## 🟡 Spot Instances
- **Discount:** Up to ~90% (cheapest)
- **Risk:** Can be terminated anytime
- **Use case:**
  - Batch jobs
  - Big data / ML
  - Fault-tolerant systems
- ❌ Not for critical workloads or databases

---

## 🔴 Dedicated Hosts
- **Hardware:** Entire physical server
- **Control:** Full (socket/core visibility)
- **Cost:** Most expensive
- **Use case:**
  - Compliance
  - BYOL (Bring Your Own License)

---

## 🟠 Dedicated Instances
- **Hardware:** Dedicated to you (no shared tenants)
- **Control:** Less than Dedicated Hosts
- **Use case:** Isolation without full hardware control

---

## 🟤 Capacity Reservations
- **Guarantee:** Capacity in a specific AZ
- **Billing:** On-Demand (even if unused)
- **Commitment:** None
- **Use case:** Must-have capacity (critical workloads)

---

# 🧠 Exam Traps & Key Differences

### 🔥 Spot vs On-Demand
- Spot = cheap + interruptible  
- On-Demand = stable + expensive  

---

### 🔥 RI vs Savings Plans
- RI = reserve specific instance  
- Savings Plans = commit to **spend**, more flexible  

---

### 🔥 Zonal RI vs Capacity Reservation
- **Zonal RI:** discount + capacity  
- **Capacity Reservation:** capacity only (no discount)  

---

### 🔥 Dedicated Hosts vs Instances
- **Hosts:** full physical control (BYOL)  
- **Instances:** just isolation  

---

# ⚡ Quick Decision Guide

- Unpredictable → **On-Demand**
- Long-term stable → **Reserved / Savings Plans**
- Cheap + fault-tolerant → **Spot**
- Compliance / licensing → **Dedicated Hosts**
- Guaranteed capacity → **Capacity Reservation**
