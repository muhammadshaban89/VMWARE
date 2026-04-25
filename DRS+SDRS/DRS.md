

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


# ⭐ What Is “Passthrough VM DRS Automation”?
This option controls whether **DRS is allowed to move (vMotion) VMs that have PCI passthrough devices attached**.

A VM with passthrough normally **cannot be vMotioned**, because the physical PCI device is tied to a specific ESXi host.

So this setting tells DRS:

> **Should I try to automate placement and balancing for VMs that have passthrough devices?**

---

# ⭐ Why Passthrough VMs Are Special
When a VM uses DirectPath I/O:

- The VM is directly connected to a physical PCI device  
- That device exists only on one host  
- vMotion is **not possible**  
- DRS cannot move the VM to another host  

Because of this, DRS normally **ignores** these VMs.

---

# ⭐ What This Setting Actually Does
### ✔ **Enabled**
DRS will:
- Consider passthrough VMs during initial placement  
- Try to place them on a host that has the required PCI device  
- But still **cannot vMotion them**  
- So load balancing is limited  

### ✔ **Disabled**
DRS will:
- Completely ignore passthrough VMs  
- Not try to place or balance them  
- You must manually choose the host  

---

# ⭐ When Should You Enable It?
Enable it if:

- You have **multiple hosts** with the same PCI passthrough device  
- You want DRS to automatically place the VM on a compatible host  
- You want DRS to avoid hosts that don’t have the device  

Disable it if:

- Only one host has the passthrough device  
- You want full manual control  
- You don’t want DRS to interfere with placement  

---

# ⭐ Simple Summary
**DRS = Automatic compute load balancing using vMotion.**  
It keeps your cluster healthy, balanced, and efficient.

---

Additional Options:
--------

# 🧩 **1. VM Distribution**

This setting tells DRS to **spread VMs evenly across hosts**, focusing on *availability* rather than performance.

### ✔ What it does
- Forces a more even **VM count** across hosts  
- Prevents too many VMs from piling onto one host  
- Helps avoid “all eggs in one basket” situations  
- Useful for licensing, compliance, or predictable host usage

### ✖ Side effects
- VMware warns: **“may see a degradation of DRS when utilizing this setting.”**  
  Meaning:
  - DRS may ignore performance‑based decisions  
  - You may get unnecessary vMotions  
  - Hosts may run hotter even if others are idle  
  - Performance balancing becomes weaker

### When to enable
- VDI clusters  
- Environments where VM count per host matters  
- When you want predictable distribution for licensing  
- When availability > performance

### When to avoid
- Performance‑sensitive clusters  
- Clusters with mixed hardware  
- High‑density compute clusters  
- Database or latency‑sensitive workloads

---

# 🧩 **2. CPU Over‑Commitment**
This setting controls the **maximum allowed vCPU:pCPU ratio** for VM placement.

### ✔ What it does
- Lets you define a ratio like:  
  - **4:1** → 4 vCPUs per 1 physical core  
  - **8:1** → 8 vCPUs per 1 physical core  
- DRS will **block VM placement** if the ratio would be exceeded  
- Prevents CPU saturation  
- Ensures predictable performance  
- Helps avoid CPU Ready spikes

### ✖ Side effects
- If ratio is too strict (e.g., 1:1), you may block VM power‑on  
- If ratio is too high, you may cause CPU contention  
- Can reduce consolidation ratio

### When to enable
- Production clusters  
- Database servers  
- Application servers  
- Latency‑sensitive workloads  
- When you want to prevent over‑provisioning

### When to disable
- Test/dev  
- VDI  
- Environments with high consolidation  
- When you want maximum density

---

# 🧩 **3. Scalable Shares**
This setting changes how **resource pool shares** behave as the cluster grows or shrinks.

### ✔ What it does
- Automatically scales shares based on the number of VMs  
- Prevents share imbalance  
- Ensures fair resource distribution  
- Makes resource pools behave consistently as VMs are added/removed  
- Recommended by VMware for most environments

### ✖ Side effects
- None significant  
- Only avoid if you have a very custom share design

### When to enable
- Almost always  
- Large clusters  
- Clusters with many resource pools  
- Environments where VMs are frequently added/removed

### When to disable
- Rare cases with strict manual share tuning  
- Legacy clusters with fixed share hierarchy

---

# 📌 **Summary Table**

| Setting | What It Controls | Best For | Avoid When |
|--------|------------------|----------|------------|
| **VM Distribution** | Even VM spread across hosts | VDI, availability-focused | Performance-sensitive clusters |
| **CPU Over-Commitment** | Max vCPU:pCPU ratio | Production, DB, latency-sensitive | Test/dev, high-density |
| **Scalable Shares** | Auto-scaling resource pool shares | Most environments | Custom share designs |

---


# ⚡ **vSphere DPM (Distributed Power Management) — What It Is**
DPM automatically **powers ESXi hosts ON or OFF** based on cluster load.

- When load is low → DPM powers off hosts to save energy  
- When load increases → DPM powers hosts back on  

It works together with DRS and HA.

---

# 🧩 **Power Management Options (DPM Settings)**

These are the settings you see under **Cluster → Configure → vSphere DRS → Power Management**.

---

## **1. Enable DPM**
This toggle turns Distributed Power Management ON or OFF.

### ✔ When Enabled:
- vCenter can automatically consolidate VMs onto fewer hosts  
- Idle hosts can be placed into **standby mode**  
- Hosts can be powered on when needed  

### ✖ When Disabled:
- No host power automation  
- All hosts stay powered on all the time  

---

## **2. Automation Level**
Controls how DPM behaves.

### **A. Manual Mode**
- vCenter *recommends* host power-on/off actions  
- You must approve them  
- Good for testing DPM behavior before going automatic

### **B. Automatic Mode**
- vCenter automatically powers hosts on/off  
- No approval needed  
- Best for stable, predictable workloads

---

## **3. DPM Threshold Slider**
This slider controls how aggressively DPM consolidates workloads.

### Levels:
- **Most Conservative**  
  - Only powers off hosts when cluster is *very* underutilized  
  - Minimal vMotions  
  - Safest for production

- **Moderate**  
  - Balanced consolidation  
  - Good for general workloads

- **Most Aggressive**  
  - Powers off hosts frequently  
  - Maximum energy savings  
  - More vMotions  
  - Not recommended for latency‑sensitive workloads

---

## **4. Host Power Management (BIOS/ESXi)**
This is separate from DPM but related.

### ESXi Power Policy Options:
- **High Performance**  
  - No CPU power saving  
  - Best for latency‑sensitive workloads  
  - Higher power usage

- **Balanced** (Default)  
  - ESXi dynamically adjusts CPU frequency  
  - Good balance of performance and power

- **Low Power**  
  - Maximum power savings  
  - Lower performance  
  - Rarely used in production

---

## **5. Host Standby Configuration**
DPM uses **standby mode** to power off hosts.

You must configure:
- **IPMI / iLO / DRAC**  
- **Wake-on-LAN** (if supported)  
- **vCenter permissions**  

If these are not configured correctly:
- DPM will fail to power hosts on  
- You will see DPM errors in vCenter

---

# 🧠 **How DPM Works Internally**
1. DRS analyzes cluster load  
2. If load is low → DRS migrates VMs to fewer hosts  
3. DPM powers off the empty hosts  
4. When load increases → DPM powers hosts back on  
5. DRS redistributes VMs  

This is why DRS and DPM must be enabled together.

---

# 📌 **Best‑Practice Recommendations**
For production clusters:

### ✔ Recommended:
- **Enable DPM**  
- **Automation: Manual (initially), then Automatic**  
- **Threshold: Conservative or Moderate**  
- **Host Power Policy: High Performance**  
- Ensure **IPMI/iLO/DRAC** is configured correctly  

### ✖ Avoid:
- Aggressive DPM on clusters with:  
  - Databases  
  - Real‑time apps  
  - High‑latency sensitivity  
  - Large VMs  
  - vSAN clusters (DPM is NOT recommended for vSAN)

---

