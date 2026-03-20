# 🧠 EC2 Purchasing Options = Problems They Solve

## 🟢 On-Demand  
> ❓ *“I don’t know what will happen”*

- Unpredictable traffic  
- Short-term usage  
- Need instant start/stop  

✅ **Solution:** maximum flexibility  
❌ **Tradeoff:** expensive  

---

## 🔵 Reserved Instances (RI)  
> 📊 *“I know exactly what I’ll use”*

- Steady workload (e.g. database)  
- Long-running systems  
- Predictable capacity  

✅ **Solution:** big discount for committing  
❌ **Tradeoff:** low flexibility  

---

## 🟣 Savings Plans  
> 🔄 *“I know I’ll use compute, but not exactly how”*

- Predictable usage but changing configs  
- Might switch instance types  

✅ **Solution:** discount + flexibility  
❌ **Tradeoff:** time commitment  

---

## 🟡 Spot Instances  
> 💸 *“I want the cheapest compute possible”*

- Can tolerate interruptions  
- Distributed / fault-tolerant systems  

✅ **Solution:** massive cost savings  
❌ **Tradeoff:** can be terminated anytime  

---

## 🔴 Dedicated Hosts  
> ⚖️ *“I have licensing or compliance constraints”*

- BYOL (per-core, per-socket licenses)  
- Regulatory requirements  

✅ **Solution:** full physical server control  
❌ **Tradeoff:** very expensive  

---

## 🟠 Dedicated Instances  
> 🔐 *“I need isolation, but not full hardware control”*

- No shared hardware with other customers  

✅ **Solution:** tenant isolation  
❌ **Tradeoff:** less control than hosts  

---

## 🟤 Capacity Reservations  
> 🚨 *“I MUST have capacity available”*

- Critical workloads  
- Need guaranteed capacity in AZ  

✅ **Solution:** capacity guarantee  
❌ **Tradeoff:** no discount, pay even if unused  

---

# ⚡ Ultra-Compact Mental Model

- Don’t know usage → **On-Demand**  
- Know usage → **Reserved / Savings Plans**  
- Want cheapest → **Spot**  
- Need compliance → **Dedicated Hosts**  
- Need isolation → **Dedicated Instances**  
- Need guaranteed capacity → **Capacity Reservations**  

---

# 🧪 Quick Decision Framework

Ask yourself:

1. Is usage predictable?  
2. Can the workload be interrupted?  
3. Do I need guaranteed capacity or compliance?

## 🔹 Spot Instance Requests
- Up to **90% cheaper** than On-Demand  
- You define a **max price**  
- The instance runs while: **spot_price < max_price**
  - Price **fluctuates dynamically** (supply/demand)

### ⛔ Interruptions
- If: **spot_price > max_price**
→ AWS may:
- stop
- terminate

- You get a **2-minute warning**

### 🧠 Typical Use Cases
- Batch jobs  
- Data processing  
- Fault-tolerant workloads  

### ❌ Not suitable for
- Databases  
- Critical systems

## 🔹 Spot Fleet
> A set of Spot Instances (+ optional On-Demand Instances)

- Define a **target capacity**
- AWS fulfills it using multiple pools

### 🔧 You can define:
- Instance types (e.g., `m5.large`)
- Availability Zones
- OS
- Multiple pools

### 🧠 Behavior
- AWS selects instances based on:
- price
- available capacity

- Stops launching when:
- target capacity is reached
- max cost is reached

---

## ⚙️ Allocation Strategies

- **lowestPrice**  
→ cheapest  
→ less stable  

- **diversified**  
→ spread across pools  
→ higher availability  

- **capacityOptimized**  
→ prioritizes pools with capacity  

- **priceCapacityOptimized (RECOMMENDED)**  
→ balances price + capacity  

---

## 🔹 Spot Block (Legacy ⚠️)
> Not commonly used anymore

- “Reserve” Spot for:
- 1 to 6 hours  
- **No interruptions** during that window (in most cases)
- May still be reclaimed in rare situations

### 🧠 Typical Use Cases (historical)
- Short batch jobs  
- Time-bound processing workloads  

---

## 🧩 What is each concept?

| Concept              | Description |
|----------------------|------------|
| Spot Instance        | Discounted instance type |
| Spot Request         | Individual Spot request |
| Spot Fleet           | Managed group of Spot instances |
| Spot Block           | Legacy fixed-duration Spot |

---

## ⚡ TL;DR
- `run-instances` → On-Demand  
- Spot → cheaper but interruptible  
- Spot Fleet → automated orchestration  
- Spot Block → legacy, mostly ignore   
  
