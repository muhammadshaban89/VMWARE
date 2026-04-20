

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

# ⭐ Simple Summary (Easy to Remember)
**SDRS = Automatic Storage Load Balancing**  
- Groups datastores into a cluster  
- Balances space + I/O  
- Moves VMDKs using Storage vMotion  
- Prevents datastore overload  
- Works like DRS, but for storage  

---

