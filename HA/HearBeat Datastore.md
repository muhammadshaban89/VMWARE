

# ⭐ **What Is a Heartbeat Datastore in vSphere HA?**
A **Heartbeat Datastore** is a **backup communication channel** used by vSphere HA when the **management network fails**.

Think of it like this:

> If hosts cannot talk over the network, they use shared datastores to “check each other’s pulse.”

This prevents **false failovers**, where VMs get restarted even though the host is actually healthy.

---

# ⭐ **Why Do We Need Heartbeat Datastores?**
Because HA must decide:

- Is the host **dead**?
- Is the host **isolated**?
- Is the host **alive but network is down**?

Without datastore heartbeats, HA might **incorrectly restart VMs** on other hosts.

---

# ⭐ **How Heartbeat Datastores Work**
Each ESXi host writes a small “I am alive” file on shared datastores.

If the HA network is down:

- Host A checks if Host B is still updating its datastore heartbeat file  
- If yes → Host B is alive  
- If no → Host B is isolated or dead → HA may restart VMs on another host  

This mechanism prevents **split‑brain** situations.

---

# ⭐ **How Many Heartbeat Datastores Are Required?**
VMware requires:

- **Minimum: 2 shared datastores per host**

More is fine, but 2 is enough.

In your cluster:
- `new-NFS` → shared by 3 hosts  
- `New-vSAN` → shared by 3 hosts  

Perfect configuration.

---

# ⭐ **Heartbeat Datastore Selection Policies**
You saw 3 options in your screenshot. Here’s what they mean:

### 1️⃣ **Automatically select datastores accessible from the hosts**
- vCenter picks the best 2 datastores  
- Easiest and safest for most environments  

### 2️⃣ **Use datastores only from the specified list**  
(You selected this option)

- You manually choose which datastores HA uses  
- HA will **not** add any others  
- Good for strict control  
- Risky if you select only 1 datastore  

### 3️⃣ **Use datastores from the specified list and complement automatically if needed**
- You choose preferred datastores  
- vCenter adds more if required  
- Best balance of control + safety  

---

# ⭐ **Which Option Should You Use?**
For your environment (NFS + vSAN), the best choice is:

### ✔ **Use datastores from the specified list and complement automatically if needed**

Why?
- Ensures HA uses your shared, stable datastores  
- Adds extra datastores automatically if needed  
- Avoids misconfiguration or outages  

---

# ⭐ Summary (Simple Version)
- Heartbeat datastores = backup communication when network fails  
- Prevent false VM restarts  
- Need at least 2 shared datastores  
- Your selected datastores (NFS + vSAN) are perfect  
- Best policy = **specified list + auto complement**

---


