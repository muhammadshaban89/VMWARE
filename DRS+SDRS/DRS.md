

# ⭐ What Is DRS?
**DRS = Distributed Resource Scheduler**  
It is a vSphere feature that **automatically balances CPU and Memory load** across ESXi hosts in a cluster.

Think of it as:

> **Automatic load balancing for compute resources.**

DRS uses **vMotion** to move VMs between hosts with **zero downtime**.

---

# ⭐ What DRS Actually Does
DRS continuously monitors:

- CPU usage  
- Memory usage  
- Host contention  
- VM demand  
- Resource pools  
- Affinity rules  

When imbalance is detected, DRS:

- Recommends a vMotion  
**or**
- Automatically performs vMotion (if automation is enabled)

---

# ⭐ Key Functions of DRS
### 1️⃣ **Initial Placement**
When you power on a VM, DRS chooses the best host based on cluster load.

### 2️⃣ **Load Balancing**
If one host becomes overloaded, DRS moves VMs to other hosts.

### 3️⃣ **Resource Pool Enforcement**
Ensures resource pool shares/limits are respected.

### 4️⃣ **Affinity / Anti‑Affinity Rules**
You can control VM placement:
- Keep VMs together  
- Keep VMs separated  
- Keep VMs on specific hosts  

### 5️⃣ **Host Maintenance Mode**
When you put a host in maintenance mode:
- DRS automatically evacuates all VMs using vMotion.

---

# ⭐ DRS Automation Levels
### ✔ **Fully Automated**
- DRS automatically moves VMs  
- Best for production clusters  

### ✔ **Partially Automated**
- Initial placement is automatic  
- Balancing requires admin approval  

### ✔ **Manual**
- DRS only gives recommendations  
- Admin must approve every move  

---

# ⭐ When DRS Helps the Most
- Clusters with **uneven workloads**  
- Environments with **many VMs**  
- Hosts with different hardware capacities  
- When avoiding performance bottlenecks is critical  

---

# ⭐ DRS vs SDRS (Quick Comparison)
| Feature | DRS | SDRS |
|--------|------|-------|
| Balances | CPU + Memory | Storage (I/O + space) |
| Moves | VMs (vMotion) | VMDKs (Storage vMotion) |
| Works on | Hosts | Datastores |
| Purpose | Compute load balancing | Storage load balancing |

---

# ⭐ Simple Summary
**DRS = Automatic compute load balancing using vMotion.**  
It keeps your cluster healthy, balanced, and efficient.

---
