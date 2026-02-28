# 🧪 3‑VM Security Sandbox on macOS with UTM

This README walks through setting up a 3‑VM sandbox on a Mac using UTM with:
- One Windows 11 VM  
- One Ubuntu VM  
- One Metasploitable 2 VM  

Use this only in a contained lab environment and for legal, educational purposes.

---

## 📋 Prerequisites

- macOS with enough resources  
  - Recommended: 16 GB RAM, 120–150 GB free disk  
- UTM installed (from the Mac App Store or https://mac.getutm.app)  
- Basic terminal familiarity for one command (`qemu-img`)

---

## 📂 Downloads Needed

Before creating any VMs, download these:

### 1. Windows 11 (ARM) ISO

- Download a Windows 11 ARM ISO from a legitimate source.  
- Save the ISO somewhere like `~/Downloads/ISOs`.

### 2. Ubuntu (ARM) ISO

- Download an Ubuntu ARM (ARM64 / aarch64) ISO (Server or Desktop).  
- Save it in `~/Downloads/ISOs`.

### 3. Metasploitable 2 Disk

- Download the Metasploitable 2 VMware image (ZIP with a `.vmdk` file).  
- Extract the ZIP so you have a `.vmdk` file, for example:
  - `Metasploitable.vmdk`

Later, you’ll convert this to `qcow2` for use in UTM.

---

## 1️⃣ Create the Windows 11 VM

These steps assume an Apple Silicon Mac and a Windows 11 ARM ISO.

1. Open **UTM**.
2. Click **“Create a New Virtual Machine”** (or the **+** icon).
3. Choose **Virtualize**.
4. Select **Windows**.
5. Check **“Install Windows 10 or higher”**.
6. For **Boot ISO Image**, click **Browse** and select your Windows 11 ARM ISO.
7. Click **Continue**.
8. Configure **Hardware**:
   - CPU: `4` cores (adjust if your Mac is limited).
   - Memory: `8 GB` (or less if RAM is tight).
9. Click **Continue**.
10. Configure **Storage**:
    - Disk size: at least `64 GB`.
11. Click **Continue**, name the VM (e.g., `Windows 11`), then **Save**.
12. Start the VM (select it and click **Play**).
13. Complete the standard Windows 11 installation (language, keyboard, account, etc.).
14. Once at the desktop, install UTM guest tools if offered (better drivers/integration).

At this point, you should have a working Windows 11 VM in UTM.

> 💡 Optional: Take a screenshot of the VM settings/summary in UTM for documentation.

---

## 2️⃣ Create the Ubuntu VM

1. In **UTM**, click **“Create a New Virtual Machine”** (or **+**).
2. Choose **Virtualize**.
3. Select **Linux**.
4. For **Boot ISO Image**, click **Browse** and select your Ubuntu ARM ISO.
5. Click **Continue**.
6. Configure **Hardware**:
   - CPU: `2–4` cores.
   - Memory: `4–8 GB` (4 GB is fine for a light server).
7. Click **Continue**.
8. Configure **Storage**:
   - Disk size: `32–64 GB`.
9. Click **Continue**, name the VM (e.g., `Ubuntu`), then **Save**.
10. Start the VM and boot from the Ubuntu ISO.
11. Follow the Ubuntu installer:
    - Choose language and keyboard.
    - Set hostname, user, and password.
    - Use the default partitioning/storage layout.
    - Finish installation and reboot.
12. Log in to Ubuntu after the reboot.

Now you have an Ubuntu VM running inside UTM.

> 💡 Optional: Capture a screenshot of the Ubuntu VM or UTM settings for your README or notes.

---

## 3️⃣ Convert Metasploitable 2 Disk

UTM works best with `qcow2` disks, so convert the `.vmdk` image using `qemu-img`.

1. Install **Homebrew** if you don’t have it:

   ```bash
   /bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh)"
2. Install **qemu** using Homebrew:

   ```bash
   brew install qemu
3. Open **Terminal** and change to the folder containing the Metasploitable `.vmdk` file:

   ```bash
   cd /path/to/metasploitable/folder
4. Convert the `.vmdk` to `qcow2`:

   ```bash
   qemu-ing convert -f vmdk -O qcow2 Metasploitable.vmdk Metasploitable.qcow2
You will attach `Metasploitable.qcow2` to a new VM in UTM.

---

## 4️⃣ Create Metasploutable 2 VM

Metasploitable 2 is an x86 image, so you must **Emulate** (not Virtualize). This will be slower but works.
1. Open **UTM** and click **"Create a New Virtual Machine** (or +).
2. Choose **Emulate**.
3. For **System / Operating System**, choose a generic x86 Linux / "Other" entry (depending on UTM version).
4. Configure **Hardware**:
   - CPU: `1-2` cores.
   - Memory: `512 MB - 1MB`.
5. In the **Storage** section:
   - Do**not** create a new empty disk.
   - Instead, add an **existing disk** and select `Metasploitable.qcow2`.
6. Leaves other defaults (no ISO required; it already has an OS).
7. Name the VM (e.g., `Metasploitable 2) and click **Save**.
8. Start the VM.
9. When it boots tp a login prompt, log in with the default credentials (commonly `msfadmin/msfadmin`-verify with Metasploitable's documentation).

Now Metasploitable 2 is running under UTM.
  >⚠️ Metasploitable is intentionally vulnerable. Keep it isolated and never expose it directly to untrusted networks.


