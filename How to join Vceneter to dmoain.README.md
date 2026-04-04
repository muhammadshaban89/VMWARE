# 🏷️ Join vCenter Server (VCSA) to an Active Directory Domain  
This applies to **vCenter 7.x, 8.x** (including 8.0.3).

---

# ✅ **Step 1 — Make sure DNS is correct**
vCenter **must** resolve your domain controllers.

On VCSA shell (SSH or console):

```
nslookup dc1.domain.local
nslookup domain.local
```

If DNS is wrong, fix it here:

**VAMI (https://vcenter:5480) → Networking → Address → DNS**

---

# ✅ **Step 2 — Set the correct NTP time**
Time mismatch = domain join failure.

In VAMI:

**https://vcenter:5480 → Time → NTP Servers**

Add your DC or pool.ntp.org.

---

# ✅ **Step 3 — Join vCenter to the domain**
Now the actual join:

**vSphere Client → Administration → Single Sign-On → Configuration → Active Directory Domain**

Choose:

### **Option 1: Active Directory (Integrated Windows Authentication)**  
This is the recommended method.

Fill in:

- **Domain**: `domain.local`
- **Username**: `administrator@domain.local` or `DOMAIN\administrator`
- **Password**

Click **Join**.

You will see:

> Successfully joined the domain.

---

# 🔄 **Step 4 — Reboot vCenter**
This is required for SSO services to reinitialize.

From VAMI:

**https://vcenter:5480 → Summary → Reboot**

After reboot check all services status:

```
service-control --status
```
- if nit started , restart all services:
```
service-control --stop --all
service-control --start --all
```

- Verify Domain:
```
/usr/lib/vmware-vmafd/bin/vmafd-cli get-domain-name --server-name localhost
```

---

# 🧩 **Step 5 — Add the domain as an Identity Source**
After reboot:

**vSphere Client → Administration → Single Sign-On → Configuration → Identity Sources**

If your domain is not listed:

1. Click **Add**
2. Choose **Active Directory (Integrated Windows Authentication)**
3. Domain: `domain.local`

Save.

---

# 🔐 **Step 6 — Grant AD users/groups permissions**
Joining the domain does NOT automatically allow login.

Go to:

**Menu → Administration → Access Control → Global Permissions**

1. Click **Add**
2. Select your domain
3. Search for:
   - A user (`muhammad@domain.local`)
   - Or a group (`vSphere-Admins`)
4. Assign **Administrator** (or any role)
5. Check **Propagate to children**

---

# 🧪 **Step 7 — Log in using AD credentials**
Use one of these formats:

- `muhammad@domain.local` (best)
- `DOMAIN\muhammad`
- `muhammad` (only if domain is default)

---

# 🛠️ If the domain join fails
Here are the most common causes:

### **1. DNS mismatch**
vCenter must use the same DNS as the domain.

### **2. Time difference > 5 minutes**
Fix NTP.

### **3. Wrong domain admin credentials**
Use a domain account with permission to join computers.

### **4. Firewall blocking ports**
Required ports: **TCP 88, 389, 636, 3268, 3269, 445, 464**

---


 **once vCenter successfully joins the domain**, giving your `VCPLAB\Administrator` (or any AD user/group) permission to log in
---

# 🟦 **Step 1 — Add the Active Directory identity source in vCenter**
After the domain join succeeds:

1. Log in to vCenter using the **local SSO admin**:
   ```
   administrator@vsphere.local
   ```
2. Go to:
   **Menu → Administration → Single Sign On → Configuration**
3. Select **Identity Sources**
4. Click **Add**
5. Choose:
   **Active Directory (Integrated Windows Authentication)**  
   (This option appears ONLY after domain join)

It should automatically detect:
- Domain: `VCPLAB.com`
- Forest: `VCPLAB.com`

Click **Save**.

Now vCenter can “see” your AD users and groups.

---

# 🟦 **Step 2 — Assign permissions to the AD Administrator**
Now you give your AD user access to vCenter.

1. Go to:
   **Menu → Administration → Access Control → Global Permissions**
2. Click **Add**
3. In the user selector:
   - Domain: **VCPLAB.COM**
   - User: **Administrator**  
     (or any AD group like `Domain Admins`)

4. Assign a role:
   - **Administrator** (full access)

5. Check:
   ✔ **Propagate to children**

6. Click **OK**

Now your AD Administrator has full vCenter permissions.

---

# 🟦 **Step 3 — Log in using AD credentials**
Now you can log in to vCenter using:

```
VCPLAB\Administrator
```
or
```
Administrator@VCPLAB.com
```

Both will work.

---

# 🟦 **Recommended Best Practice**
Instead of giving permissions to the single user `Administrator`, give permissions to the AD group:

```
VCPLAB\Domain Admins
```

This way:

- Any domain admin can log in
- You don’t need to modify vCenter permissions again
- It follows VMware best practices

---

# 🟢 **Summary**
After domain join:

| Task | What to do |
|------|------------|
| Add identity source | AD (Integrated Windows Auth) |
| Assign permissions | Global Permissions → Add → VCPLAB\Domain Admins |
| Role | Administrator |
| Login | VCPLAB\Administrator |


---
