

# ⭐ **1. Hot Migration (vMotion)**  
This is the **most common** and the one you use during DRS and vLCM remediation.

### 🔥 What it is  
Moving a **powered‑on VM** from one ESXi host to another **with zero downtime**.

### 🔧 Requirements  
- vMotion network configured  
- Shared storage (unless using Storage vMotion)  
- Compatible CPU (or EVC enabled)  
- VM must be powered ON  

### 🧠 When it’s used  
- DRS load balancing  
- Host entering Maintenance Mode  
- vLCM remediation  
- Manual migrations  
- Avoiding host failures  

### ✔ Key benefit  
**No downtime.** Users don’t notice anything.

---

# ⭐ **2. Cold Migration**  
This is the simplest type.

### ❄️ What it is  
Moving a **powered‑off VM** between:  
- Hosts  
- Clusters  
- Datastores  
- vCenters (if Enhanced Linked Mode)

### 🔧 Requirements  
- VM must be **powered OFF**  
- No vMotion network required  
- No CPU compatibility required  
- No shared storage required  

### 🧠 When it’s used  
- Moving templates  
- Moving VMs between clusters with incompatible CPUs  
- Moving VMs between datastores without Storage vMotion  
- When VM is shut down for maintenance  

### ✔ Key benefit  
**Works even when hosts are incompatible.**

---

# ⭐ **3. Suspended Migration**  
This is the least used but important.

### 💤 What it is  
VM is **suspended (paused)** → memory state is written to disk → VM is moved → VM resumes on the destination host.

### 🔧 Requirements  
- VM must be **suspended**  
- No CPU compatibility required  
- No shared storage required  

### 🧠 When it’s used  
- When you cannot power off the VM  
- When CPU families are incompatible  
- When vMotion is not possible  
- When you need to move a VM but want to avoid a full shutdown  

### ⚠️ Downside  
**There is downtime** — the VM is paused during the move.

---

# ⭐ **Side-by-Side Comparison**

| Migration Type | VM State | Downtime | CPU Compatibility Needed | Shared Storage Needed | Typical Use |
|----------------|----------|----------|---------------------------|------------------------|--------------|
| **Hot (vMotion)** | Powered ON | ❌ No | ✔ Yes (or EVC) | ✔ Yes | DRS, Maintenance Mode, vLCM |
| **Cold** | Powered OFF | ✔ Yes | ❌ No | ❌ No | Templates, incompatible hosts |
| **Suspended** | Suspended | ✔ Yes (short) | ❌ No | ❌ No | When vMotion not possible |

---

# ⭐ **Which one does vLCM use?**
**Always Hot Migration (vMotion)**  
Because vLCM requires the host to be empty before remediation.

If vMotion fails → remediation fails.

---


If you want, I can also explain **Storage vMotion**, **Cross‑vCenter vMotion**, or **EVC impact on migrations**.
