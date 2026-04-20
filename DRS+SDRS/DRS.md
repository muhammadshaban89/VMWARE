

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




# ⭐ 1. Fully Automated DRS
This means:

> vCenter automatically moves VMs between hosts using vMotion to balance CPU and memory.

You don’t need to approve anything — DRS handles it.

This is the recommended mode for almost all clusters.

---

# ⭐ 2. Migration Threshold (Level 3)
This slider controls **how aggressively** DRS moves VMs.

### Levels:
- **1–2 = Conservative**  
  Only moves VMs when imbalance is severe  
- **3 = Default / Balanced**  
  Moves VMs when moderately imbalanced  
- **4–5 = Aggressive**  
  Moves VMs frequently to keep hosts perfectly balanced  

Your setting (**Level 3**) is ideal for:

- Stable workloads  
- Production clusters  
- Avoiding unnecessary vMotions  

---

# ⭐ 3. Predictive DRS (Disabled)
Predictive DRS uses **vRealize Operations Manager (vROps)** to predict future load and move VMs *before* a host becomes overloaded.

You don’t have it enabled — which is fine unless you use vROps.

---

# ⭐ 4. VM Automation (Enabled)
This means:

> All VMs follow the cluster’s automation level (Fully Automated).

If this was disabled, you could override automation per VM.

---

# ⭐ 5. VM Device Stun Time Limit (Disabled)
This is an advanced setting that prevents long stun times during Storage vMotion.

Most environments leave this **disabled** unless they have very large disks.

During vMotion or Storage vMotion, VMware must briefly freeze the VM to finalize the migration.
This freeze is called a stun.
• 	Normal stun: 1–50 ms
• 	Long stun: hundreds of ms to several seconds
• 	Very long stun: 10+ seconds (bad)
If the stun is too long, users notice lag, apps freeze, or the VM becomes unresponsive.

---



# ⭐ Simple Summary
**DRS = Automatic compute load balancing using vMotion.**  
It keeps your cluster healthy, balanced, and efficient.

---
