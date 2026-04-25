

# ⭐ What SDRS (Storage DRS) Actually Does
Storage DRS monitors two key metrics across datastores:  
- **Space usage** (capacity)  
- **I/O latency** (performance)  


When a datastore becomes too full or too slow, SDRS recommends or automatically performs **Storage vMotion** to move VM disks (VMDKs) to healthier datastores.  


---

# ⭐ Core Functions of SDRS
### 1️⃣ **Initial Placement**
When you create or clone a VM, SDRS chooses the best datastore based on:  
- Free space  
- I/O load  


### 2️⃣ **Load Balancing**
SDRS continuously checks datastore health and moves VMDKs if:  
- Space > 80% (default threshold)  
- I/O latency > 15 ms (default threshold)  


### 3️⃣ **Datastore Maintenance Mode**
You can put a datastore into maintenance mode → SDRS automatically evacuates all VM disks using Storage vMotion.  


### 4️⃣ **Affinity / Anti‑Affinity Rules**
You can control VM disk placement:  
- Keep disks together  
- Keep disks separated  


---

# ⭐ What Is a Datastore Cluster?
A **Datastore Cluster** is a logical group of VMFS or NFS datastores that SDRS manages as one storage pool.  


SDRS cannot mix VMFS + NFS in the same cluster.  


---

# ⭐ SDRS Modes
### ✔ **Manual Mode**
- SDRS gives recommendations  
- Admin approves moves  


### ✔ **Automatic Mode**
- SDRS automatically performs Storage vMotion  
- Best for large environments  


---

# ⭐ Why SDRS Is Useful
- Prevents datastores from filling up  
- Avoids performance bottlenecks  
- Reduces admin workload  
- Ensures optimal VM disk placement  


---

# ⭐ Requirements
- vCenter Server 5.0+  
- ESXi 5.0+  
- Enterprise Plus license  
- Shared VMFS or NFS datastores  

---

# 🧩 Storage DRS Automation (Step 2)**  
This screen defines *how much control* Storage DRS has over storage placement and Storage vMotion operations.

## **1. Cluster Automation Level**
Controls whether SDRS only recommends or automatically performs migrations.

### **Options:**
- **No Automation (Manual Mode)**  
  SDRS *only recommends* migrations. You must approve each Storage vMotion.  
  Useful when:  
  - You want full admin control  
  - You have strict storage policies  
  - You want to observe SDRS behavior first  

- **Fully Automated (Selected)**  
  SDRS automatically performs:  
  - Initial placement  
  - Storage vMotion for balancing  
  - Space and I/O load balancing  
  Best for production clusters where automation is desired.

---

## **2. Space Balance Automation Level**
Controls how SDRS handles datastore *capacity imbalance*.

### **Options:**
- **No Automation**  
  Only recommendations, no automatic moves.
- **Fully Automated (Selected)**  
  SDRS automatically moves VMDKs when a datastore reaches the space threshold.

---

## **3. I/O Balance Automation Level**
Controls how SDRS handles datastore *performance imbalance*.

### **Options:**
- **No Automation**  
  Only recommendations.
- **Fully Automated (Selected)**  
  SDRS automatically moves VMDKs when datastore latency exceeds the I/O threshold.

---

## **4. Rule Enforcement Automation Level**
Controls enforcement of SDRS affinity/anti‑affinity rules.

### **Options:**
- **No Automation**  
  Rules are checked but not enforced.
- **Fully Automated (Selected)**  
  SDRS automatically moves disks to maintain rule compliance.

---

## **5. Policy Enforcement Automation Level**
Controls enforcement of SPBM (Storage Policy Based Management).

### **Options:**
- **No Automation**  
  SDRS will not auto‑correct policy violations.
- **Fully Automated (Selected)**  
  SDRS automatically moves disks to datastores that satisfy the VM’s storage policy.

---

# 🧩 Storage DRS Runtime Settings (Step 3)**  
These settings define *when* SDRS should take action.

---

## **1. Enable I/O Metric for SDRS Recommendations**
- **ON (Selected)**  
  SDRS considers datastore latency when making decisions.  
  Without this, SDRS only considers *space*.

---

## **2. I/O Latency Threshold**
Default: **15 ms** (your screenshot shows 15 ms)  
Range: **5–100 ms**

Meaning:  
If datastore latency exceeds this threshold, SDRS considers it “congested” and may migrate VMDKs.

Lower value = more aggressive I/O balancing  
Higher value = fewer migrations

---

## **3. Space Threshold**
SDRS triggers balancing when a datastore reaches this threshold.

### **Two ways to define it:**

### **A. Utilized Space (%) — 80% (Selected)**  
If datastore usage exceeds 80%, SDRS will move VMDKs to free space.

### **B. Minimum Free Space (GB)**  
Example: **1 GB**  
SDRS ensures at least this amount of free space remains.

You can use either or both.

---

# 📌 **Summary Table**

| Setting | Purpose | Your Value | Alternatives |
|--------|---------|------------|--------------|
| Cluster Automation | Controls SDRS actions | Fully Automated | Manual |
| Space Balance | Capacity balancing | Fully Automated | Manual |
| I/O Balance | Latency balancing | Fully Automated | Manual |
| Rule Enforcement | Affinity rules | Fully Automated | Manual |
| Policy Enforcement | SPBM compliance | Fully Automated | Manual |
| I/O Metric | Enable latency checks | Enabled | Disabled |
| I/O Latency Threshold | Trigger for I/O balancing | 15 ms | 5–100 ms |
| Space Threshold | Trigger for space balancing | 80% | 50–100% or min free GB |

---


# ⭐ Simple Summary (Easy to Remember)
**SDRS = Automatic Storage Load Balancing**  
- Groups datastores into a cluster  
- Balances space + I/O  
- Moves VMDKs using Storage vMotion  
- Prevents datastore overload  
- Works like DRS, but for storage  

---

