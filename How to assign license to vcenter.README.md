
# 🔑 How to Assign a License to vCenter Server

### 1. Log in to vCenter
Use the vSphere Client (HTML5 UI):

```
https://<vcenter-fqdn-or-ip>/
```

You must log in with an account that has **Global > Licenses** privileges. 

---

# 2. Go to the Licensing Section
In the left navigation:

**Menu → Administration → Licensing → Licenses**  
Here you will see your current evaluation license.

---

# 3. Add Your License Key
Click:

**ADD → Enter your vCenter license key → Next → Finish**  
This imports the license into the vCenter inventory. 

---

# 4. Assign the License to vCenter
Now go to:

**Administration → Licensing → Assets → vCenter Server Systems**

1. Select your vCenter instance  
2. Click **Assign License**  
3. Choose the license you added  
4. Click **OK**  

The license is now applied and the evaluation warning disappears. 

---

# 🧭 Quick Reference Table

| Task | Where to Click | Notes |
|------|----------------|-------|
| Add license key | Administration → Licensing → Licenses → **ADD** | Supports multiple keys |
| Assign to vCenter | Administration → Licensing → Assets → **vCenter Server Systems** | Must select the vCenter object |
| Check license status | Same page under “Usage” | Shows expiration, capacity, features |

---

# 📝 Tips for Your Environment
- After your VCSA filesystem repair and reboot, give vCenter **5–10 minutes** to fully start all services before assigning licenses.
- If the Licensing page shows “No assets,” refresh the browser or restart the vSphere Client session.
- ESXi hosts are licensed separately under **Assets → Hosts**.

--
