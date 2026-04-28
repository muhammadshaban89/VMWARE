Step By Step Guide to upgrade  host using vLCM.
---

# ⭐ **PHASE 1 — Import ESXi ISO into Lifecycle Manager**

### 1. Open Lifecycle Manager
- vCenter → **Menu** → **Lifecycle Manager**

### 2. Go to Image Depot
- Left side → **Image Depot**
- You will see:
  - ESXi base images  
  - Vendor add-ons  
  - Components  
  - Imported ISOs  

### 3. Import the ESXi 8.0 U3 ISO
- Click **Import ISO**
- Select the **ESXi 8.0 U3 ISO** file
- vLCM will:
  - Upload the ISO  
  - Extract the ESXi base image  
  - Add it to the depot  

**Important:**  
The ISO is stored **inside vCenter**, not on a datastore.

---

# ⭐ **PHASE 2 — Create the vLCM Cluster Image**

### 1. Go to your cluster
- vCenter → **Hosts and Clusters** → select your **cluster**

### 2. Open the Image tab
- Cluster → **Updates** → **Image**

### 3. Start image setup
- Click **Setup Image** (or **Edit** if already configured)

### 4. Select the ESXi Base Image
- Under **ESXi Version**, choose:
  - **8.0 U3 – 24022510** (or whichever build you imported)

### 5. Select Vendor Add-on (if applicable)
- Dell → Dell OEM Add-on  
- HPE → HPE Add-on  
- Lenovo → Lenovo Add-on  

If you don’t have OEM integration, you can skip this.

### 6. Components (optional)
- Leave default unless you need specific drivers.

### 7. Firmware Add-on (only if OEM integration exists)
- If you have OMIVV / OneView / XClarity → select firmware add-on  
- If not → skip

### 8. Validate the image
- Click **Validate**  
- Fix any warnings if needed

### 9. Finish image setup
- Click **FINISH IMAGE SETUP**

Now your cluster is officially managed by vLCM image.

---

# ⭐ **PHASE 3 — Check Compliance**

### 1. Click **CHECK COMPLIANCE**
You will see:

- **ESXi 8.0 U3 hosts → Compliant**
- **ESXi 7.0 U2 host → Non-compliant**

This is correct.

---

# ⭐ **PHASE 4 — Understand the 3 Upgrade Actions**

These appear **after** image setup is finished.

## 1️⃣ **Pre-check**
A dry-run validation.

- No reboot  
- No Maintenance Mode  
- No changes  

It checks:

- vMotion readiness  
- DRS capacity  
- HA state  
- Storage/vSAN health  
- HCL compatibility  
- Firmware compatibility  
- Maintenance Mode feasibility  

**Use this FIRST.**

---

## 2️⃣ **Stage**
Copies the ESXi 8.0 U3 image to the host **without installing it**.

- No reboot  
- No Maintenance Mode  
- No upgrade  

Useful when:

- You want to preload the image during business hours  
- You want faster remediation later  

**Optional step.**

---

## 3️⃣ **Remediate**
This is the **actual upgrade**.

vLCM will:

1. vMotion VMs off the host  
2. Enter Maintenance Mode  
3. Install ESXi 8.0 U3  
4. Install drivers  
5. Install firmware (if OEM integration)  
6. Reboot the host  
7. Exit Maintenance Mode  
8. Mark host as compliant  

**This is the final upgrade step.**

---

# ⭐ **PHASE 5 — Upgrade the ESXi 7.0 U2 Host**

### 1. Select the 7.0 U2 host
Cluster → Updates → Image → **Hosts**

### 2. Run Pre-check
- Click **Pre-check**
- Ensure all checks pass

### 3. (Optional) Stage the image
- Click **Stage**
- This copies the upgrade payload to the host

### 4. Remediate the host
- Click **Remediate**
- Confirm the ESXi 8.0 U3 image
- Start remediation

vLCM will now:

- vMotion VMs  
- Enter Maintenance Mode  
- Upgrade ESXi  
- Reboot  
- Exit Maintenance Mode  

You do **NOT** manually put the host in Maintenance Mode.

---

# ⭐ **PHASE 6 — Post-Upgrade Validation**

After remediation:

### 1. Verify ESXi version
Host → Summary → **ESXi 8.0 U3**

### 2. Check compliance
Cluster → Updates → Image → **CHECK COMPLIANCE**

### 3. Test functionality
- vMotion test  
- Storage adapters  
- Network adapters  
- Hardware health  

---

# ⭐ **PHASE 7 — Cluster Fully Upgraded**

Once the 7.0 U2 host is upgraded:

- All 3 hosts are **ESXi 8.0 U3**  
- All hosts show **Compliant**  
- Cluster is fully consistent  
- vLCM image is now your single source of truth  

---



Just tell me what you want next.
