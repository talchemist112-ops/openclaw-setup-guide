# Step 3: Clone and Build OpenClaw

## What you'll do

Download the OpenClaw source code from GitHub and compile it into a runnable program.

---

## Prerequisites

- Completed [Step 2: Dependencies](02-dependencies.md)
- Ubuntu terminal is open

---

## Steps

### 3.1 — Create a projects directory

Let's make a folder to keep things organized:

```bash
mkdir -p ~/projects
cd ~/projects
```

**What this does**: Creates a `projects` folder in your home directory and moves into it.

### 3.2 — Clone the OpenClaw repository

```bash
git clone https://github.com/pjasicek/OpenClaw.git
```

**What this does**: Downloads the complete OpenClaw source code from GitHub.

**Expected output**:
```
Cloning into 'OpenClaw'...
remote: Enumerating objects: ...
Receiving objects: 100% ...
Resolving deltas: 100% ...
```

### 3.3 — Navigate into the OpenClaw folder

```bash
cd OpenClaw
```

### 3.4 — Look at what's inside (optional)

```bash
ls -la
```

**Expected output**: You'll see folders like `Build_Release`, `OpenClaw`, `ThirdParty`, and files like `CMakeLists.txt`, `README.md`.

### 3.5 — Create a build directory

```bash
mkdir -p build
cd build
```

**What this does**: Creates a separate folder for compiled files. This keeps the source code clean.

### 3.6 — Configure the build with CMake

```bash
cmake ..
```

**What this does**: CMake reads the `CMakeLists.txt` file and figures out how to compile OpenClaw on your system. The `..` means "look in the parent folder for the instructions."

**Expected output**: Lots of lines starting with `-- `. At the end you should see:
```
-- Configuring done
-- Generating done
-- Build files have been written to: /home/salman/projects/OpenClaw/build
```

> **If you see errors**: Make sure all dependencies from Step 2 are installed. The most common issue is a missing SDL2 package.

### 3.7 — Compile OpenClaw

```bash
make -j$(nproc)
```

**What this does**:
- `make` — Runs the actual compilation, turning C++ source code into a program
- `-j$(nproc)` — Uses all your CPU cores to compile faster (`nproc` returns the number of cores)

**Expected output**: Many lines of compilation. This may take **2-10 minutes** depending on your machine. At the end you should see no errors. Look for:
```
[100%] Built target openclaw
```

> **If compilation fails**: Read the error message carefully. Common fixes:
> - Missing library → Go back to [Step 2](02-dependencies.md) and install all packages
> - Out of memory → Try `make -j2` instead (uses fewer cores)

### 3.8 — Verify the build

```bash
ls -la openclaw
```

**Expected output**: A file named `openclaw` with execute permissions:
```
-rwxr-xr-x 1 salman salman 12345678 ... openclaw
```

The exact size will vary, but you should see the file exists.

Also check the Build_Release directory:

```bash
ls ../Build_Release/
```

**Expected output**: You should see files like `config.xml`, an `ASSETS` folder, and possibly other game-related files.

---

## Verify success

```bash
# Check the binary exists and is executable
file openclaw
```

**Expected output** (something like):
```
openclaw: ELF 64-bit LSB executable, x86-64, ...
```

This confirms OpenClaw has been compiled successfully.

---

## What just happened (summary)

1. We **downloaded** the complete OpenClaw source code (C++ files)
2. **CMake** analyzed the code and created a build plan
3. **make** compiled all the C++ code into a single executable program called `openclaw`

The game engine is now ready — but it needs the original game files to actually run. That's the next step.

---

**Next step**: [04-game-files.md](04-game-files.md) — Get and place the original game files
