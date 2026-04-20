
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

