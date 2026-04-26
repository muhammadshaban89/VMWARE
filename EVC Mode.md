**EVC (Enhanced vMotion Compatibility)** standardizes CPU features across ESXi hosts so VMs can vMotion between different CPU generations without errors. 

It works by masking newer CPU instructions and exposing only a common baseline.

🧩 **CPU instructions**

are the low‑level operations that a processor is physically capable of performing.

They are the “language” the CPU understands — the smallest building blocks of all software execution.

Understanding CPU instructions is the key to understanding why EVC exists and why different CPU generations are incompatible without it.

Let’s break it down clearly and practically.

Without EVC → vMotion fails with CPU mismatch errors.

A CPU instruction is a single operation the processor can execute, such as:

• 	Add two numbers

• 	Move data from memory to a register

• 	Encrypt data

• 	Perform vector math

• 	Compress or decompress data

• 	Execute floating‑point operations

Every CPU generation adds new instructions that older CPUs do not have.

---


# 🧩 What EVC Actually Does  

EVC ensures that **all hosts in a cluster present the same CPU instruction set** to virtual machines.  

This allows vMotion to work even when hosts have different CPU generations.  

VMware confirms that EVC enforces a **CPUID baseline** so VMs see a uniform CPU feature set.  


# 🧠 How EVC Works (Internals)  
EVC uses the CPU’s **CPUID** interface to detect supported instruction sets (SSE4.2, AVX, AES‑NI, etc.).  
It then **masks** any instructions that exceed the selected EVC baseline.  


Example:  
- Host A: Intel Haswell (supports AVX2)  
- Host B: Intel Sandy Bridge (no AVX2)  

If EVC baseline = **Sandy Bridge**, then:  
- Host A hides AVX2  
- Host B already matches  
- All VMs see Sandy Bridge features  
- vMotion works between both hosts  

---

# 🧩 Why EVC Is Needed  
VMware states that EVC allows **mixed CPU generations** in the same cluster and enables vMotion across them.  


This is critical when:  
- Adding new hosts to an old cluster  
- Performing rolling upgrades  
- Running VDI (Instant Clones require consistent CPU features)  
- Migrating workloads without downtime  

---

# 🧩 EVC Modes (Examples)

## **Intel EVC Examples**
| EVC Mode | CPU Generation | Example CPUs |
|----------|----------------|--------------|
| Merom | 2006 | Xeon 51xx |
| Penryn | 2007 | Xeon 52xx |
| Nehalem | 2008 | Xeon 55xx |
| Westmere | 2010 | Xeon 56xx |
| Sandy Bridge | 2011 | Xeon E5‑26xx v1 |
| Ivy Bridge | 2012 | Xeon E5‑26xx v2 |
| Haswell | 2013 | Xeon E5‑26xx v3 |
| Broadwell | 2014 | Xeon E5‑26xx v4 |
| Skylake | 2017 | Xeon Scalable Gen1 |

Example:  
If your cluster has:  
- Host 1: Xeon E5‑2680 v2 (Ivy Bridge)  
- Host 2: Xeon E5‑2680 v3 (Haswell)  

You must set EVC to **Ivy Bridge** so both hosts match.

---

# 🧩 Per‑VM EVC (Important)  
VMware explains that Per‑VM EVC applies EVC at the **VM level**, not the cluster.  

How Per‑VM EVC Works?
Per‑VM EVC sets a minimum CPU feature requirement for that VM.
This requirement must be met by any host the VM runs on.
VMware states:
• 	Per‑VM EVC is a VM attribute, not a cluster attribute.
• 	It allows VMs to migrate across clusters with different CPU generations.
• 	It can be higher than the cluster EVC mode.
• 	But the VM can only power on or vMotion to hosts that support that higher baseline.

🧩 Detailed Example
Cluster Hosts:
• 	Host A → Sandy Bridge
• 	Host B → Ivy Bridge
• 	Host C → Haswell
Cluster EVC = Sandy Bridge
This ensures:
• 	All hosts can join the cluster
• 	All VMs can vMotion between all hosts (if they use Sandy Bridge features only)
VM Per‑VM EVC = Haswell
This VM now requires:
• 	AVX2
• 	FMA3
• 	Other Haswell‑level instructions
Result:
• 	VM can run on Host C (Haswell)
• 	VM cannot run on Host A or B
• 	vMotion will only allow migration to Haswell‑capable hosts
• 	Cluster EVC does NOT restrict this VM’s CPU features
This behavior is explicitly documented by VMware.

🧩 Why VMware Created Per‑VM EVC
VMware introduced Per‑VM EVC to solve a major problem:

Per‑VM EVC allows:
• 	High‑performance VMs to use newer CPU features
• 	Older hosts to remain in the cluster
• 	VMs to migrate across clusters and vCenters
• 	Horizon Instant Clones to maintain compatibility
VMware confirms that Per‑VM EVC “enables true mobility for VMs” across different CPU generations.

🧩 Important Rules to Remember
✔ A VM’s Per‑VM EVC mode can be higher than the cluster EVC
But only if the host supports it.
✔ A VM with Per‑VM EVC = Haswell
Cannot run on Sandy Bridge or Ivy Bridge hosts.
✔ Per‑VM EVC requires:
• 	VM must be powered off to change mode
• 	VM hardware version 14 or later
• 	vSphere 6.7+


---

# 🧩 Real‑World Examples

## **Example 1 — Mixed Hardware Cluster**
Hosts:  
- Dell R720 (Ivy Bridge)  
- Dell R730 (Haswell)  
- Dell R740 (Skylake)

Correct EVC baseline: **Ivy Bridge**  
Why?  
All hosts support Ivy Bridge instructions, but not all support Haswell or Skylake.

---

## **Example 2 — Adding New Hosts**
Existing cluster: Sandy Bridge  
New host: Skylake  

If EVC = Sandy Bridge:  
- New Skylake host joins cluster  
- Skylake features are masked  
- VMs can vMotion freely  


---

## **Example 3 — Without EVC**
VM starts on Haswell host → uses AVX2  
You try to vMotion to Sandy Bridge host → **fails**  
Because Sandy Bridge cannot run AVX2 instructions.

---

# 🧩 How to Enable EVC (Cluster Level)
1. Power off VMs **only if raising** EVC level  
2. vCenter → Cluster → Configure → VMware EVC  
3. Select CPU vendor (Intel/AMD)  
4. Choose baseline  
5. Enable  

VMware confirms that enabling EVC on existing clusters with powered‑on VMs may require shutdown depending on direction.  


---


# 🧠 **How We Determine the Oldest Generation (General Rule) - for example -intel CPUs**

### ✔ Rule 1 — Look at the microarchitecture name  
Intel publishes the order of architectures.  
Older architecture = fewer CPU instructions = lowest EVC baseline.

### ✔ Rule 2 — Look at the Xeon version number  
For Intel Xeon E5 CPUs:

- v1 → Sandy Bridge  
- v2 → Ivy Bridge  
- v3 → Haswell  
- v4 → Broadwell  

So **v2 is older than v3**, and **v3 is older than v4**.

### ✔ Rule 3 — Look at the Scalable CPU generation  
For Intel Scalable CPUs:

- 1st Gen (41xx/61xx) → Skylake  
- 2nd Gen (42xx/62xx) → Cascade Lake  
- 3rd Gen (53xx/63xx) → Ice Lake  

So **41xx < 42xx < 53xx**.

---

# 🧩 **Real Example**

> How do we know Ivy Bridge is the oldest?

Let’s compare:

- **Ivy Bridge** → 2012  
- **Haswell** → 2013  
- **Skylake** → 2017  

Intel released Ivy Bridge **before** Haswell and Skylake.  
Therefore, Ivy Bridge is the **oldest generation** in that list.

---

# 🔍 **How vCenter Confirms This Automatically**

vCenter → Cluster → Configure → VMware EVC

It will show:
- Ivy Bridge = supported by all hosts  
- Haswell = NOT supported by Ivy Bridge hosts  
- Skylake = NOT supported by Ivy Bridge or Haswell hosts  

So the **highest baseline that all hosts support** is Ivy Bridge.

---

# 🧠 **Shortcut: How YOU can always know the oldest generation**

### ✔ If CPUs are E5‑26xx:
- v1 = Sandy Bridge  
- v2 = Ivy Bridge  
- v3 = Haswell  
- v4 = Broadwell  

### ✔ If CPUs are Silver/Gold:
- 41xx = Skylake  
- 42xx = Cascade Lake  
- 53xx = Ice Lake  

### ✔ The lowest number = the oldest generation  
→ That becomes your EVC baseline.

---

