# Step 2: Install Build Tools and Libraries

## What you'll do

Install everything Ubuntu needs to download, compile, and run OpenClaw — including a C++ compiler, build tools, and the SDL2 graphics/audio libraries.

---

## Prerequisites

- Completed [Step 1: WSL Setup](01-wsl-setup.md)
- Ubuntu terminal is open and working

---

## Steps

### 2.1 — Open your Ubuntu terminal

Search for **Ubuntu** in the Windows Start menu and open it. You should see:
```
salman@YOURPC:~$
```

### 2.2 — Install essential build tools

```bash
sudo apt install -y build-essential cmake git
```

**What each package does**:
- `build-essential` — Installs the **GCC C++ compiler** and **make** (tools that turn source code into a runnable program)
- `cmake` — A build system that OpenClaw uses to configure its compilation
- `git` — A tool to download source code from GitHub

**Expected output**: You'll see packages downloading and installing. It ends with something like `Setting up build-essential...`

### 2.3 — Install SDL2 libraries

SDL2 is a set of libraries that OpenClaw uses for graphics, sound, fonts, and input.

```bash
sudo apt install -y libsdl2-dev libsdl2-image-dev libsdl2-mixer-dev libsdl2-ttf-dev libsdl2-gfx-dev
```

**What each package does**:
| Package | Purpose |
|---------|---------|
| `libsdl2-dev` | Core graphics, window management, keyboard/mouse input |
| `libsdl2-image-dev` | Loading image files (PNG, JPG, etc.) |
| `libsdl2-mixer-dev` | Playing sound effects and music |
| `libsdl2-ttf-dev` | Rendering text and fonts |
| `libsdl2-gfx-dev` | Drawing shapes and graphics primitives |

**Expected output**: More packages downloading. Ends with `Setting up libsdl2-gfx-dev...` or similar.

### 2.4 — Install TinyXML (optional but recommended)

```bash
sudo apt install -y libtinyxml-dev
```

**What this does**: Installs a library for reading XML configuration files. If you skip this, OpenClaw will compile its own copy — but installing it avoids potential issues.

### 2.5 — Install audio support for background music

OpenClaw uses MIDI music. To hear background music, you need a MIDI synthesizer:

```bash
sudo apt install -y timidity freepats
```

**What each package does**:
- `timidity` — A software MIDI synthesizer that plays music files
- `freepats` — Free instrument sound samples that timidity uses

### 2.6 — (Optional) Install a display server for GUI

Since WSL doesn't have a display by default, you need a way to show the game window.

**If you're on Windows 11 or Windows Server 2022 with recent updates**, WSLg (built-in GUI support) may already work. Test it:

```bash
sudo apt install -y x11-apps
xclock
```

If a clock window appears on your Windows desktop, **WSLg is working** — skip to the verification step.

**If no window appears**, install an X server on Windows:

1. On your **Windows** side, download and install **VcXsrv** from: https://sourceforge.net/projects/vcxsrv/
2. Launch **XLaunch** from the Start menu
3. Choose these settings:
   - Display number: `0`
   - Select **Multiple windows**
   - Click **Next**, then **Next**, then check **Disable access control**
   - Click **Finish**
4. Back in Ubuntu, set the display variable:

```bash
echo 'export DISPLAY=$(cat /etc/resolv.conf | grep nameserver | awk "{print \$2}"):0' >> ~/.bashrc
source ~/.bashrc
```

5. Test again:
```bash
xclock
```

A clock should now appear on your Windows desktop.

---

## Verify success

Run these commands to confirm everything is installed:

```bash
# Check C++ compiler
g++ --version
```
**Expected**: Shows version info like `g++ (Ubuntu 11.x.x) 11.x.x`

```bash
# Check cmake
cmake --version
```
**Expected**: Shows `cmake version 3.x.x`

```bash
# Check git
git --version
```
**Expected**: Shows `git version 2.x.x`

```bash
# Check SDL2 is installed
dpkg -l | grep libsdl2
```
**Expected**: Multiple lines showing `ii  libsdl2-...` packages

---

## What you installed (summary)

| Category | Packages | Why |
|----------|----------|-----|
| Build tools | build-essential, cmake, git | Compile code and download source |
| Graphics & audio | libsdl2-dev + 4 extensions | Game window, images, sound, fonts |
| XML parsing | libtinyxml-dev | Read config files |
| Music | timidity, freepats | Play MIDI background music |
| Display | x11-apps (or VcXsrv) | Show game window on Windows |

---

**Next step**: [03-clone-and-build.md](03-clone-and-build.md) — Download and compile OpenClaw
