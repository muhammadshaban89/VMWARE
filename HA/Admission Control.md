
# ⭐ **vSphere HA – Admission Control (Full Explanation)**

Admission Control determines **whether ESXi is allowed to power on new VMs** based on the cluster’s ability to survive host failures.

If Admission Control says **NO**, the VM will not power on — even if resources appear free — because HA must reserve capacity for failover.

Let’s go through each option in your screenshot.

---

# 1️⃣ **Host failures cluster tolerates**
This defines **how many hosts can fail** while still guaranteeing that all VMs can restart on the remaining hosts.

Example:  
If you have **2 hosts**, the maximum value is **1** 

Meaning:  
If 1 host fails, the other host must have enough reserved CPU & RAM to restart all VMs.

---

# 2️⃣ **Define host failover capacity by**
This dropdown controls **how Admission Control calculates reserved resources**.

You have selected:

### ✔ **Cluster Resource Percentage**
This is the most flexible and recommended method.

It means:
- vSphere calculates how much CPU & RAM must be reserved  
- Based on the number of host failures you selected  

Other methods (for your awareness):
- **Slot Policy** (old method, not recommended)
- **Dedicated Failover Hosts** (rarely used)

---

# 3️⃣ **Override calculated failover capacity**
This is an **advanced override** that lets you manually set the reserved CPU and RAM percentages.

You have it **ON**, with:

- **59% CPU reserved**
- **82% Memory reserved**

Meaning:
- ESXi will NOT power on new VMs if doing so reduces available failover capacity below these percentages.

Why override?
- Sometimes VMware’s automatic calculation is too conservative  
- Or you want to fine‑tune based on your workload behavior  

---

# 4️⃣ **Reserved failover CPU capacity**
This is the **percentage of total cluster CPU** that must remain free for failover.

Example:  
If your cluster has 100 GHz CPU total:  
59% reserved = 59 GHz must remain unused.

This ensures that if a host fails, the remaining hosts have enough CPU to restart all VMs.

---

# 5️⃣ **Reserved failover Memory capacity**
Same logic as CPU, but for RAM.

Example:  
If your cluster has 256 GB RAM total:  
82% reserved = 210 GB must remain unused.

This is why you may see “Insufficient resources” when powering on VMs — HA is protecting failover capacity.

---

# 6️⃣ **Reserve Persistent Memory failover capacity**
This applies only if you use **PMem (NVDIMM / Intel Optane)**.

Since most clusters don’t use PMem, it’s OFF.

If ON:
- HA reserves PMem capacity for failover  
- Similar to CPU/RAM logic  

---

# 7️⃣ **Override calculated Persistent Memory failover capacity**
Same idea as CPU/RAM override, but for PMem.

You have it OFF, which is normal.

---

# ⭐ Summary Table

| Setting | Meaning | Your Value | Impact |
|--------|---------|------------|--------|
| Host failures cluster tolerates | How many hosts can fail | 1 | Cluster must survive 1 host failure |
| Define failover capacity by | Calculation method | Cluster Resource % | Best practice |
| Override failover capacity | Manual control | ON | You set CPU/RAM reservation manually |
| Reserved CPU | CPU reserved for failover | 59% | Limits VM power‑on |
| Reserved Memory | RAM reserved for failover | 82% | Limits VM power‑on |
| PMem settings | For persistent memory | OFF | Normal unless using PMem |

---

