**Host Profiles let you capture the full configuration of a “reference ESXi host” and apply it to other hosts so they all become identical.** They are used to standardize, audit, and automatically remediate ESXi host settings across a cluster. 

---

# 🧩 What a Host Profile Is  
A **Host Profile** is a template extracted from a fully configured ESXi host.  
It contains settings such as:

- Networking (vSwitches, VMkernel ports, VLANs)  
- Storage (iSCSI, NFS, adapters, multipathing)  
- Security (SSH, lockdown mode, firewall rules)  
- NTP/time settings  
- Hostname, DNS, IP schema  
- User accounts and passwords  
- PCI devices, services, and advanced settings  

This profile becomes the **baseline** for all other hosts.  
When a host deviates, vCenter marks it **non‑compliant** and can automatically fix it. 

---

# 🧩 Why Host Profiles Are Used  
- **Standardize ESXi configuration** across clusters  
- **Eliminate configuration drift**  
- **Automate host provisioning**  
- **Ensure compliance** for audits  
- **Integrate with Auto Deploy** for stateless hosts  
- **Speed up adding new hosts** (no manual setup)  

---

# 🧩 Requirements  
- vCenter Server  
- ESXi hosts managed by vCenter  
- **vSphere Enterprise Plus license** (Host Profiles are not available in Standard) 

---

# 🟦 How to Configure Host Profiles (Step‑by‑Step)

## **STEP 1 — Choose a Reference Host**
Pick one ESXi host that is **fully configured exactly the way you want**.  
This becomes the “golden host.” 

---

## **STEP 2 — Extract the Host Profile**
1. vSphere Client → **Menu → Policies and Profiles → Host Profiles**  
2. Click **Extract Host Profile**  
3. Select the reference host  
4. Name the profile (e.g., “ASIR2‑Baseline‑Profile”)  
5. Finish  

This creates the Host Profile template. 

---

## **STEP 3 — Attach the Profile to Hosts or a Cluster**
1. Right‑click the Host Profile → **Attach/Detach**  
2. Select:
   - Individual hosts  
   - Or the entire cluster  
3. Click **Attach**  

---

## **STEP 4 — Customize Host‑Specific Settings**
Some settings must be unique per host:

- Hostname  
- Management IP  
- VMkernel IPs  
- iSCSI IQN  
- MAC addresses  
- Root password (optional)  

Host Profiles allow you to mark these as **customizable** so vCenter prompts you for values. 

---

## **STEP 5 — Check Compliance**
1. Select the host  
2. Click **Check Host Profile Compliance**  
3. vCenter shows:
   - Compliant  
   - Non‑compliant  
   - Unknown  

---

## **STEP 6 — Remediate Non‑Compliant Hosts**
1. Select the host  
2. Click **Remediate**  
3. Review changes  
4. Apply  

Some remediations require a **host reboot**. 

---

# 🧩 What Host Profiles Can Configure (Examples)
According to VMware documentation:  
- **Identity settings** (hostname, DNS, domain)  
- **Networking** (vSwitches, vDS, NICs, VLANs)  
- **Storage** (HBA, iSCSI, NFS, SATP/PSP)  
- **Security** (SSH, lockdown mode, firewall)  
- **NTP/time zone**  
- **User accounts**  
- **Advanced system settings**  
- **PCI passthrough**  
- **Services and daemons**  


---

# 🧩 Host Profiles vs Configuration Profiles (vSphere 8+)
VMware is replacing Host Profiles with **Configuration Profiles**, which are more automated and easier to manage.  
But Host Profiles remain fully supported. 

---
