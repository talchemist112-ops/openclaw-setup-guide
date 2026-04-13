# Step 1: Install WSL2 and Ubuntu

## What you'll do

Install **WSL2** (Windows Subsystem for Linux) and **Ubuntu** on your Windows machine. WSL lets you run a full Linux environment inside Windows — this is where we'll build and run OpenClaw.

## Why WSL?

OpenClaw was primarily built for Linux. While it can compile on Windows too, the Linux build path is simpler, better documented, and has fewer dependency headaches. WSL gives us the best of both worlds — Linux tools inside Windows.

---

## Prerequisites

- Windows 10 version 2004 or later / Windows 11 / Windows Server 2022
- Administrator access on your machine
- Internet connection

---

## Steps

### 1.1 — Open PowerShell as Administrator

- Press **Windows key**, type `PowerShell`
- Right-click **Windows PowerShell** and choose **Run as administrator**
- Click **Yes** if prompted by User Account Control

### 1.2 — Update WSL (if already partially installed)

Run this command to make sure WSL is up to date:

```powershell
wsl --update
```

**Expected output**: Something like `Installing: Windows Subsystem for Linux` or `No update available` if already current.

### 1.3 — Set WSL2 as default version

```powershell
wsl --set-default-version 2
```

**Expected output**: `For information on key differences with WSL 2 please visit...`

### 1.4 — Install Ubuntu

```powershell
wsl --install -d Ubuntu
```

**Expected output**: It will download Ubuntu (this may take 5-10 minutes depending on your internet). You'll see progress messages.

> **Note**: If the above command fails, try `wsl --install -d Ubuntu-22.04` instead.

### 1.5 — Restart your computer

After the installation finishes, **restart your computer**. This is required for WSL2 to fully activate.

### 1.6 — First launch — Create your Linux user

After restart:

1. Open the **Start menu**, search for **Ubuntu**, and click on it
2. A terminal window opens. Wait for it to finish setting up (this takes 1-2 minutes the first time)
3. It will ask you to create a username — type a simple username (e.g., `salman`) and press Enter
4. It will ask for a password — type a password and press Enter (you won't see characters as you type — that's normal)
5. Confirm the password

**Expected output** (after setup):
```
salman@YOURPC:~$
```

This means Ubuntu is running and ready.

### 1.7 — Verify WSL2 is working

Back in **PowerShell** (as admin), run:

```powershell
wsl --list --verbose
```

**Expected output** — you should see something like:
```
  NAME      STATE           VERSION
* Ubuntu    Running         2
```

The **VERSION** column should say **2**. If it says **1**, run:
```powershell
wsl --set-version Ubuntu 2
```

### 1.8 — Update Ubuntu packages

Inside the Ubuntu terminal, run:

```bash
sudo apt update && sudo apt upgrade -y
```

**What this does**: Downloads the latest package lists and upgrades all installed software to the latest versions.

**Expected output**: Lots of text scrolling by. It will end with something like `X upgraded, Y newly installed...`

> **Tip**: `sudo` means "run as administrator". It will ask for your Linux password (the one you created in step 1.6).

---

## Verify success

Run this inside Ubuntu:

```bash
lsb_release -a
```

**Expected output** (something like):
```
Distributor ID: Ubuntu
Description:    Ubuntu 22.04.x LTS
Release:        22.04
Codename:       jammy
```

If you see this, Ubuntu is installed and working.

---

## Quick tips

- **Open Ubuntu anytime**: Search for "Ubuntu" in the Start menu
- **Access Windows files from Ubuntu**: Your Windows drives are at `/mnt/c/`, `/mnt/d/`, etc.
- **Access Ubuntu files from Windows**: In File Explorer, type `\\wsl$\Ubuntu` in the address bar
- **Close Ubuntu**: Type `exit` or just close the window

---

**Next step**: [02-dependencies.md](02-dependencies.md) — Install build tools and libraries
