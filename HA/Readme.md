
# ⭐ **vSphere HA – Failures and Responses (Full Explanation)**

## 1️⃣ **Enable Host Monitoring**
**What it does:**  
Allows ESXi hosts to send and receive HA heartbeats.  
If a host stops sending heartbeats, HA considers it failed.

**If disabled:**  
HA will NOT restart VMs even if a host crashes.

**When to keep ON:**  
Always ON unless doing maintenance or network changes.

---

# 2️⃣ **Host Failure Response**
**Options:**  
- **Restart VMs**  
- **Disabled**  
- **Power off and restart VMs** (rare cases)

**What it means:**  
If a host *actually fails* (power loss, PSOD, hardware crash), HA restarts the VMs on another healthy host.

**Recommended:**  
✔ **Restart VMs**

---

# 3️⃣ **Response for Host Isolation**
This is triggered when a host is **alive** but **cannot reach the HA network**.

Host isolation ≠ host failure.

**Options:**
- **Disabled**  
- **Power off and restart VMs**  
- **Shut down and restart VMs**

### What each does:
- **Disabled:**  
  Host does nothing. VMs keep running on the isolated host.  
  (Risk: split‑brain if storage is still accessible)

- **Power off and restart VMs:**  
  Host powers off VMs → HA restarts them on another host.

- **Shut down and restart VMs:**  
  Graceful shutdown → restart on another host.

**Recommended:**  
If using shared storage → **Power off and restart VMs**

---

# 4️⃣ **Datastore with PDL (Permanent Device Loss)**
PDL = Storage is permanently gone (e.g., LUN removed, cable unplugged, array failure).

**Options:**
- **Disabled**  
- **Power off and restart VMs**

### What it means:
- **Power off and restart VMs:**  
  If storage is permanently lost, HA powers off affected VMs and restarts them on a host with working storage.

**Recommended:**  
✔ **Power off and restart VMs**

---

# 5️⃣ **Datastore with APD (All Paths Down)**
APD = Storage temporarily unreachable (network issue, controller reboot, etc.)

**Options:**
- **Disabled**  
- **Power off and restart VMs – Conservative**  
- **Power off and restart VMs – Aggressive**

### Difference between Conservative vs Aggressive:
- **Conservative:**  
  HA restarts VMs *only if it is sure* another host has access to the datastore.  
  Safer, avoids VM loss.

- **Aggressive:**  
  HA restarts VMs even if it is *not sure* other hosts have access.  
  Faster recovery but risk of restart failure.

**Recommended:**  
✔ **Conservative** (default and safest)

---

# 6️⃣ **VM Monitoring**
Monitors the **VM’s OS heartbeat** (VMware Tools heartbeat).

**Options:**
- **Disabled**  
- **VM Monitoring Only**  
- **VM and Application Monitoring**

### What it does:
If the VM OS stops responding (crash, freeze), HA resets the VM.

**VM and Application Monitoring:**  
Requires in‑guest application heartbeat (rarely used unless apps support it).

**Recommended:**  
Enable **VM Monitoring Only** for production workloads.

---

# 🔍 Summary Table

| Setting | What It Controls | Recommended |
|--------|------------------|-------------|
| Enable Host Monitoring | Detect host failures | ON |
| Host Failure Response | Restart VMs on host crash | Restart VMs |
| Host Isolation Response | What to do if host loses network | Power off & restart VMs |
| PDL Response | Permanent storage loss | Power off & restart VMs |
| APD Response | Temporary storage loss | Conservative |
| VM Monitoring | Restart VMs if OS freezes | VM Monitoring Only |

---

