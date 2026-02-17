# CMOS-Circuit-Design-and-SPICE-Simulations-using-Sky130

## 🖥️ VirtualBox Setup

Follow the steps below to load and run the provided CMOS Virtual Disk Image (VDI) in Oracle VirtualBox.

---

### Step 1: Install VirtualBox
Download and install Oracle VirtualBox from the official site:  
🔗 https://www.virtualbox.org/wiki/Downloads

---

### Step 2: Create the Virtual Machine
- Open **VirtualBox**
- Click **New**
- Enter the following configuration:

  - **Type:** Linux  
  - **Version:** Ubuntu 18.04 (Bionic Beaver) (64-bit)

- Click **Next** to proceed

---

### Step 3: Configure Memory
- Allocate RAM according to your system capacity  
- **Recommended:** 4096 MB  
- Click **Next**

---

### Step 4: Link the CMOS VDI
- Choose **Use an existing virtual hard disk file**
- Click the **yellow folder** button
- Navigate to the extracted CMOS `.vdi` file and select it
- Confirm and complete the VM creation (**Next → Finish**)

  <img width="800" height="500" alt="Screenshot (64)" src="https://github.com/user-attachments/assets/9f85efd5-c100-433f-b2e3-419432a33c85" />


---

### Step 5: Launch the VM
- Highlight the newly created virtual machine
- Click **Start** to boot the environment

---

> **Tip:** If the VM does not start, verify that hardware virtualization is enabled in your BIOS/UEFI settings.
