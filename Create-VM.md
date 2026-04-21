
# ⭐ **1. Create a new virtual machine**
Use this when you want to build a VM **from scratch**.

You will manually choose:

- CPU  
- RAM  
- Disk  
- Network  
- OS type  

Then you install the OS yourself (ISO, PXE, etc.).

**Use when:**  
You want a fresh VM with custom hardware settings.

---

# ⭐ **2. Deploy from template**
A **template** is a pre‑configured VM image.

Deploying from a template:

- Saves time  
- Ensures standardization  
- Automatically applies your organization’s settings  

**Use when:**  
You want to quickly deploy a VM with a standard OS + configuration.

---

# ⭐ **3. Clone an existing virtual machine**
This creates a **copy** of an existing VM.

You can clone:

- Powered‑off VM → full clone  
- Powered‑on VM → hot clone (if supported)  

**Use when:**  
You want an exact duplicate of an existing VM.

---

# ⭐ **4. Clone virtual machine to template**
This converts a VM into a **template**.

**Use when:**  
You want to create a reusable golden image.

---

# ⭐ **5. Convert template to virtual machine**
This turns a template back into a normal VM.

**Use when:**  
You need to update or modify the template.

---

# ⭐ **6. Clone template to template**
Creates a copy of an existing template.

**Use when:**  
You want multiple template versions (e.g., Windows 2022 v1, v2, etc.).

---

# ⭐ Which Option Should You Choose?
If you’re just learning or building a new VM:

👉 **Choose “Create a new virtual machine.”**

If your company uses standard images:

👉 **Choose “Deploy from template.”**

If you want a copy of an existing VM:

👉 **Choose “Clone an existing virtual machine.”**

--------

# ⭐ **What “VM Storage Policy” Means**
A **VM Storage Policy** tells vSphere **how and where** to store the VM’s disks based on:

- Performance  
- Redundancy  
- Encryption  
- vSAN rules  
- Thin/thick provisioning  
- Stretched cluster requirements  

It ensures the VM is placed on storage that matches the policy’s rules.

---

# ⭐ **What Each VM Storage Policy Means**

### ✔ **Datastore Default**  
- Uses the datastore’s default settings  
- No special rules  
- Basic option for simple environments  

### ✔ **Management Storage Policy – Encryption**  
- Stores the VM on encrypted storage  
- Requires a **KMS (Key Management Server)**  
- Used for sensitive or regulated workloads  

### ✔ **Management Storage Policy – Large**  
- Designed for VMs with **large disks**  
- Ensures placement on high‑capacity or high‑performance datastores  

### ✔ **Management Storage Policy – Regular**  
- Standard policy for normal workloads  
- Balanced performance and capacity  

### ✔ **Management Storage Policy – Single Node**  
- For **single‑node vSAN** or standalone storage  
- Used in edge or ROBO environments  

### ✔ **Management Storage Policy – Stretched**  
- For **vSAN stretched clusters**  
- Provides redundancy across two physical sites  

### ✔ **Management Storage Policy – Stretched ESA**  
- Same as above but for **vSAN ESA (Express Storage Architecture)**  

### ✔ **Management Storage Policy – Stretched Lite**  
- Reduced redundancy version  
- Lower storage overhead  
- Still provides cross‑site protection  

### ✔ **Management Storage Policy – Thin**  
- Forces **thin provisioning**  
- Saves space  
- Good for test/dev workloads  

### ✔ **VM Encryption Policy**  
- Encrypts the VM itself  
- Requires KMS  
- Protects VM files even if datastore is not encrypted  

---

# ⭐ **Other Options**

### ✔ **Encrypt this virtual machine**  
- Encrypts the VM  
- Requires KMS  
- Used for compliance/security  

### ✔ **Disable Storage DRS for this VM**  
- Prevents SDRS from moving this VM  
- Use only for special workloads (databases, latency‑sensitive apps)

---

# ⭐ **Which Policy Should YOU Choose?**

- **Normal VM → Management Storage Policy – Regular**  
- **Large disk VM → Large**  
- **Encrypted VM → VM Encryption Policy**  
- **vSAN stretched cluster → Stretched / Stretched ESA**  
- **Thin provisioning → Thin**  
- **Edge/single node → Single Node**

If you’re unsure:

👉 **Management Storage Policy – Regular** is the safest default.

---



# ⭐ **CD/DVD Drive Options Explained**

## ✔ **1. Client Device**
This means the VM will use **your local machine’s** CD/DVD or ISO.

- Uses the vSphere Web Client connection  
- You can mount an ISO from your laptop  
- Useful when installing an OS from your local ISO file  

**When to use:**  
If the ISO is on your computer, not on the datastore.

---

## ✔ **2. Datastore ISO File**
This attaches an ISO stored **inside a datastore**.

- Most common option  
- Fast, reliable  
- ISO stays available even if you disconnect  
- Works for automated deployments  

**When to use:**  
If the ISO is uploaded to a datastore (recommended for production).

---

## ✔ **3. Content Library ISO File**
This attaches an ISO stored in a **vSphere Content Library**.

- Best for standardized environments  
- Supports versioning  
- Syncs across multiple vCenters  
- Great for templates and automation  

**When to use:**  
If your organization uses a content library for OS images.

---

## ✔ **4. Host Device**
This uses the **physical CD/DVD drive of the ESXi host**.

- Rarely used today  
- Requires a physical disk inserted into the ESXi host  
- Slow and not recommended  

**When to use:**  
Almost never — only if you physically inserted a DVD into the ESXi host.

---

# ⭐ Which Option Should YOU Choose?
For 99% of VM installations:

👉 **Datastore ISO File**

It’s the fastest, most stable, and most professional method.

If you don’t have the ISO uploaded yet, then:

👉 **Client Device**  
is fine for quick installs.

---

# ⭐ **Disk Provisioning Options Explained**
---------------
⭐ What Does “Zeroing Blocks” Mean?

A virtual disk is made up of many storage blocks.
Zeroing means:

    Writing zeros (0x00) to every block on the disk before it is used.

So instead of random data, the disk starts with clean, empty blocks.

⭐ Why Zero Blocks?

Zeroing blocks is done for:

✔ Security

Old data from the datastore cannot leak into a new VM.

✔ Performance (for some disk types)

Some storage systems perform better when blocks are pre‑zeroed.

✔ vSAN / RAID consistency

Zeroed blocks help with parity and checksum calculations.


## ✔ **1. Thick Provision Lazy Zeroed**
**What it does:**
- Allocates the **full disk size immediately** (e.g., a 40GB disk consumes 40GB on datastore)
- Blocks are **zeroed only when first written**
- Faster to create, slower on first write

**Use cases:**
- General workloads  
- Lab VMs  
- When you want predictable space usage  

**Performance:** Medium  
**Space usage:** High  

---

## ✔ **2. Thick Provision Eager Zeroed**
**What it does:**
- Allocates the **full disk size immediately**
- **Zeroes all blocks at creation time**
- Best performance for storage operations

**Use cases:**
- High‑performance workloads  
- Databases  
- vSAN/vSphere HA clusters  
- When you need maximum I/O performance  

**Performance:** Highest  
**Space usage:** High  

**Note:** Takes longer to create because it zeroes the entire disk.

---

## ✔ **3. Thin Provision**
**What it does:**
- Allocates **only the space actually used**
- Grows as the VM writes data  
- Saves datastore space  
- Most flexible for labs

**Use cases:**
- Nested labs  
- Multiple ESXi hosts  
- vCenter, DC, SAN VMs  
- When datastore space is limited  

**Performance:** Lower than thick  
**Space usage:** Lowest  

**Warning:**  
If datastore becomes full → VMs can freeze.

---

### Only use Eager Zeroed Thick if:
- You are testing performance  
- You are building a vSAN cluster  
- You have plenty of storage  

---

# ⭐ Summary

| Option | Space | Performance | Best For |
|--------|--------|--------------|-----------|
| **Thin** | Low | Medium | Labs, nested ESXi, vCenter |
| **Lazy Zeroed** | High | Medium | General workloads |
| **Eager Zeroed** | High | High | Databases, vSAN, performance testing |

---


# ⭐ **Disk Mode Options Explained**

## ✔ **1. Dependent (Default & Recommended)**
This is the **normal** disk mode.

### Behavior:
- Disk **respects snapshots**
- When you take a snapshot → disk state is saved
- When you revert → disk rolls back to snapshot state

### Use cases:
- Almost all VMs  
- vCenter, ESXi nested hosts, DC, SAN  
- Any VM where you want snapshots to work

👉 **This is the mode you should use 99% of the time.**

---

## ✔ **2. Independent – Persistent**
This mode **ignores snapshots**, but **keeps all changes permanently**.

### Behavior:
- Snapshots DO NOT include this disk  
- Changes are written directly to disk  
- Reverting a snapshot does NOT revert this disk

### Use cases:
- Database disks  
- Log disks  
- High‑I/O disks  
- Disks that must never roll back  
- Backup repositories  
- SAN/NFS/iSCSI storage VMs

👉 Good when you want **consistent data** even if you revert snapshots.

---

## ✔ **3. Independent – Nonpersistent**
This mode **ignores snapshots**, and **throws away all changes** when the VM powers off.

### Behavior:
- All writes go to a temporary redo log  
- When VM powers off → all changes are deleted  
- Disk returns to original state every boot

### Use cases:
- Training labs  
- Kiosk VMs  
- Malware testing  
- Disposable VMs  
- Golden images

👉 Not recommended for production or nested ESXi labs.

---

# ⭐ **Which Disk Mode Should YOU Use in Your Lab?**

Based on your setup:

- VMware Workstation  
- Nested ESXi hosts  
- SAN VM  
- vCenter  
- Domain Controller  

Here is the correct choice for each VM:

### ✔ **ESXi Hosts → Dependent**
Snapshots are useful for testing.

### ✔ **vCenter → Dependent**
You want snapshots for rollback.

### ✔ **Domain Controller → Dependent**
Snapshots are okay in labs.

### ✔ **SAN VM → Independent – Persistent**
Why?
- You do NOT want SAN data to revert when you revert snapshots  
- NFS/iSCSI storage must stay consistent  
- Prevents datastore corruption  

### ✔ **Test VMs → Dependent or Nonpersistent**
Depending on whether you want changes saved.

---

# ⭐ Summary Table

| Disk Mode | Snapshots | Data Saved? | Best For |
|-----------|-----------|-------------|----------|
| **Dependent** | Yes | Yes | Normal VMs, labs |
| **Independent – Persistent** | No | Yes | SAN, DB, logs |
| **Independent – Nonpersistent** | No | No | Disposable VMs |


--------------

# ⭐ **Where the virtual disk (VMDK) will be stored.**

That’s all.  
It does **not** affect performance, snapshots, provisioning, or disk behavior — it only controls the **datastore path** where the disk file lives.


---

# ⭐ **Location Options Explained**

## ✔ **1. Store with the virtual machine (Recommended)**
This means:

- The VMDK disk file is stored **in the same folder** as the VM  
- Example path:  
  `/vmfs/volumes/GoldSan/VM-Name/VM-Name.vmdk`

### Why this is good:
- Clean and organized  
- Easy to move or copy the VM  
- No confusion about where disks are  
- Best for labs and nested ESXi setups  
- Works perfectly with snapshots and backups  

👉 **This is the correct choice for your lab unless you have a specific reason to separate disks.**

---

## ✔ **2. Browse…**
This lets you choose **another datastore or folder** to store the disk.

### When to use:
- You want OS disk on one datastore and data disk on another  
- You want to test performance differences (NFS vs iSCSI)  
- You want to simulate production storage separation  
- You want to store large disks on cheaper/slower storage  
- You want to put SAN VM disks on a dedicated datastore  

### Example:
- VM files on `GoldSan`  
- Data disk on `NFS-Share`  
- Logs on `iSCSI-Target`  

This is more advanced and only needed for specific scenarios.

---


# ⭐ Summary

| Location Option | Meaning | Best For |
|-----------------|---------|----------|
| **Store with the virtual machine** | Disk stored in same folder as VM | Labs, nested ESXi, simple setups |
| **Browse…** | Choose another datastore/folder | Advanced setups, performance testing |

---



# ⭐ **Disk Sharing Options Explained**

## ✔ **1. Unspecified**
This simply means:

- VMware will use the **default** behavior  
- Default = **No sharing**

It’s the same as choosing **No sharing**, just not explicitly set.

👉 Safe to leave as-is.

---

## ✔ **2. No sharing (Recommended for your lab)**
This means:

- **Only one VM** can use this disk at a time  
- If another VM tries to attach the same disk → VMware blocks it  
- Prevents corruption  
- Works with snapshots  
- Works with all disk modes  

### Use cases:
- Normal VMs  
- ESXi nested hosts  
- vCenter  
- Domain Controller  
- SAN VM  
- Any VM that does NOT need shared disks  

👉 **This is the correct choice for 99% of your VMs.**

---

## ✔ **3. Multi-writer (Advanced, special use only)**
This allows **multiple VMs to write to the same VMDK at the same time**.

This is extremely dangerous unless you know what you’re doing.

### Use cases:
- VMware vSAN (physical clusters)  
- Oracle RAC clusters  
- Microsoft Failover Clustering (shared disk)  
- High-availability database clusters  
- Shared-disk cluster testing  

### Requirements:
- Disk must be **Thick Provision Eager Zeroed**  
- Disk mode must be **Independent – Persistent**  
- Snapshots are **disabled**  
- Only supported on **VMFS** datastores  
- Not supported on NFS  

👉 **Do NOT use Multi-writer in your nested lab unless you are specifically building a shared-disk cluster.**

---

### ✔ **Use “No sharing” for all VMs.**

### ❗ Never use Multi-writer unless:
- You are building Oracle RAC  
- You are building Windows Failover Cluster with shared disks  
- You know exactly how cluster shared disks work  

---

# ⭐ Summary Table

| Sharing Option | Meaning | Best For |
|----------------|---------|----------|
| **Unspecified** | Defaults to No sharing | Normal VMs |
| **No sharing** | Only one VM can use disk | Your entire lab |
| **Multi-writer** | Multiple VMs write same disk | Advanced clustering only |

---




