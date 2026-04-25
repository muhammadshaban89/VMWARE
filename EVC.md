 **EVC (Enhanced vMotion Compatibility)** 
 -----------------------------------------
 
EVC standardizes CPU features across all ESXi hosts in a cluster by masking newer CPU instructions so every host exposes the **same baseline**.

This ensures:
- vMotion works between different CPU generations  
- Instant Clones / Horizon desktops migrate without issues  
- Cluster upgrades don’t break compatibility  
- Mixed hardware clusters remain stable  

Without EVC → you get “No compatible host” errors.

---

# 🧩 **Two Types of EVC**
## **1. Cluster‑Level EVC**
Applies to all hosts in the cluster.

Used for:
- vMotion between hosts  
- DRS  
- HA failover  
- Maintenance mode migrations  

## **2. Per‑VM EVC**
Applies to a single VM.

Used for:
- Migrating VMs across clusters  
- Using newer CPU features inside a cluster with older hosts  
- Horizon Instant Clones (recommended)

---

# 🧩 **How to Configure EVC (Cluster Level)**

### **Step 1 — Check CPU Vendor**
All hosts must be:
- Intel only  
- OR AMD only  

No mixing.

### **Step 2 — Power Off VMs (only if raising EVC level)**
Lowering EVC = no need to power off  
Raising EVC = VMs must be powered off  

### **Step 3 — Enable EVC**
vSphere Client → Cluster → Configure → VMware EVC

Choose:
- **Intel EVC Mode**  
- OR **AMD EVC Mode**

### **Step 4 — Select the Baseline**
For Intel, baselines include:
- Merom  
- Penryn  
- Nehalem  
- Westmere  
- Sandy Bridge  
- Ivy Bridge  
- Haswell  
- Broadwell  
- Skylake  

Choose the **lowest CPU generation** in your cluster.

---

# 🧩 **How to Configure Per‑VM EVC**
1. Power off the VM  
2. VM → Configure → VMware EVC  
3. Enable Per‑VM EVC  
4. Select the baseline  
5. Power on the VM  

This is extremely useful when:
- Moving VMs between clusters  
- Migrating from old hardware to new  
- Running Horizon Instant Clones  

---

# 🧩 **Best EVC Practices (Real‑World)**

### ✔ Always enable EVC **before** adding new hosts  
Prevents compatibility issues later.

### ✔ For VDI clusters:  
Use **Per‑VM EVC** (recommended by VMware)  
Why?  
Instant Clones need consistent CPU features across parent/replica/child VMs.

### ✔ For mixed hardware clusters:  
Choose the **lowest CPU generation**.

### ✔ For new clusters:  
Use **Haswell** or **Broadwell** for Intel  
These are stable and widely supported.

### ✔ Do NOT disable EVC after enabling  
You will break vMotion for running VMs.

---

# 🧩 **Common EVC Problems (and why they happen)**

### ❌ “EVC cannot be enabled because a host does not support the baseline”
One host has an older CPU → choose a lower baseline.

### ❌ “VMs must be powered off to raise EVC”
Expected behavior.

### ❌ “No compatible host” even when EVC is enabled
Usually caused by:
- 3D graphics acceleration enabled  
- VM hardware version mismatch  
- Per‑VM EVC conflicts  
- CPU features exposed by passthrough devices  

### ❌ Nested ESXi (Workstation/Proxmox)  
Nested CPUs expose inconsistent CPUID → EVC fails unless you use **Merom/Penryn**.

---

