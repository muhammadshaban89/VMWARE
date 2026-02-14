
## 1. Clean network design for your 3‑host lab

**Assumptions:**

- **3 ESXi hosts**: `esxi01`, `esxi02`, `esxi03`  
- **1 physical switch** (or L2 domain)  
- **1 vCenter (VCSA)** running on one of the hosts  
- **2 physical NICs per host** for VM traffic (vmnic0, vmnic1)

**Logical layout:**

- **Management network**: VLAN 10  
- **vMotion network**: VLAN 20  
- **VM network (production)**: VLAN 30  
- **Optional: Lab/Testing network**: VLAN 40  

**High‑level design:**

- Create **one vSphere Distributed Switch (vDS)**  
- Attach all 3 hosts to this vDS  
- Use **2 uplinks** (Uplink1, Uplink2) mapped to `vmnic0`, `vmnic1` on each host  
- Create **distributed port groups**:
  - `Mgmt-VLAN10` (VLAN 10)
  - `vMotion-VLAN20` (VLAN 20)
  - `VM-Net-VLAN30` (VLAN 30)
  - `Lab-VLAN40` (optional)

---

## 2. Whether to use vSS or vDS

**Short answer for you:**  
Use **vDS**. vSS only makes sense for single‑host or no‑vCenter setups.

**Why vDS for your 3‑host lab:**

- **Centralized config**: One place to define port groups/VLANs  
- **Consistent across hosts**: No manual per‑host mismatch  
- **vMotion friendly**: Same networks on all hosts  
- **Easier to scale**: Add more hosts later with zero extra network config  

You can keep a **small vSS** only for:
- Management + vCenter during initial deployment  
- Then migrate to vDS once vCenter is up.

---

## 3. How to configure VLANs on your physical switch

Assume a Cisco‑like syntax; adapt to your vendor.

### 3.1 Trunk ports to ESXi hosts

For each ESXi host uplink (e.g., ports `Gi1/0/1`, `Gi1/0/2`, etc.):

```bash
interface GigabitEthernet1/0/1
 description ESXi01-Uplink1
 switchport mode trunk
 switchport trunk allowed vlan 10,20,30,40
 spanning-tree portfast trunk

interface GigabitEthernet1/0/2
 description ESXi01-Uplink2
 switchport mode trunk
 switchport trunk allowed vlan 10,20,30,40
 spanning-tree portfast trunk
```

Repeat for ESXi02, ESXi03 uplink ports.

### 3.2 (Optional) Management VLAN SVI

If you want L3 gateway on the switch:

```bash
interface Vlan10
 description Management
 ip address 192.168.10.1 255.255.255.0
 no shut
```

Same idea for other VLANs if needed.

---

## 4. How to verify ESXi uplink trunking

### 4.1 On the physical switch

Check trunk status:

```bash
show interfaces trunk
show running-config interface GigabitEthernet1/0/1
```

You should see:
- Mode: trunk  
- Allowed VLANs: 10,20,30,40  

### 4.2 On ESXi (CLI via SSH)

List physical NICs:

```bash
esxcli network nic list
```

Check which vSwitch/vDS they’re attached to:

```bash
esxcli network vswitch standard list
esxcli network vswitch dvs vmware list
```

Check VLAN on port groups:

```bash
esxcli network vswitch dvs portgroup list
```

You should see:
- `Mgmt-VLAN10` → VLAN 10  
- `vMotion-VLAN20` → VLAN 20  
- `VM-Net-VLAN30` → VLAN 30  

---

## 5. How to test VM connectivity step‑by‑step

### 5.1 Management network (ESXi + vCenter)

1. **From your laptop/PC**:
   - Ping ESXi management IPs (VLAN 10)
   - Ping vCenter IP

2. **From ESXi shell**:
   ```bash
   vmkping <default-gateway-of-VLAN10>
   vmkping <vcenter-ip>
   ```

### 5.2 vMotion network

1. Ensure each host has a **vmkernel NIC** on `vMotion-VLAN20`.  
2. From ESXi shell on `esxi01`:
   ```bash
   vmkping -I vmkX <esxi02-vmotion-ip>
   vmkping -I vmkX <esxi03-vmotion-ip>
   ```
   Replace `vmkX` with the vMotion vmkernel interface.

### 5.3 VM network (across hosts)

1. Create **one test port group**: `VM-Net-VLAN30` on vDS (VLAN 30).  
2. Deploy **one test VM on each host**:
   - `vm-test-01` on `esxi01`
   - `vm-test-02` on `esxi02`
   - `vm-test-03` on `esxi03`
3. Assign IPs in same subnet, e.g.:
   - `192.168.30.11`
   - `192.168.30.12`
   - `192.168.30.13`
4. From inside `vm-test-01`:
   ```bash
   ping 192.168.30.12
   ping 192.168.30.13
   ```
5. If ping fails:
   - **Check**: correct port group on each VM  
   - **Check**: VLAN ID on port group  
   - **Check**: switch trunk allows VLAN 30  
   - **Check**: VM OS firewall (Windows firewall, iptables, etc.)

---

