# Step 4: Get and Place the Original Game Files

## What you'll do

Obtain the original Captain Claw game asset file (`CLAW.REZ`) and place it where OpenClaw expects to find it. You'll also prepare the `ASSETS.ZIP` file.

---

## Prerequisites

- Completed [Step 3: Clone and Build](03-clone-and-build.md)
- Ubuntu terminal is open

---

## Important note about CLAW.REZ

OpenClaw is an **engine only** — it does not include the game's graphics, sounds, music, or level data. Those come from the original 1997 Captain Claw game, packaged in a file called `CLAW.REZ`.

**You must legally obtain this file.** Options:
- If you own a physical copy of Captain Claw, the `CLAW.REZ` file is on the CD
- If you purchased it digitally, it's in the installation folder
- The game may be available from retro gaming stores

The `CLAW.REZ` file is typically around **170 MB** in size.

---

## Steps

### 4.1 — Locate your CLAW.REZ file

If you have Captain Claw installed on Windows, the file is usually at:
```
C:\Program Files (x86)\Captain Claw\CLAW.REZ
```
or wherever you installed the game.

### 4.2 — Copy CLAW.REZ into the OpenClaw Build_Release directory

You can access your Windows files from Ubuntu. Here's how to copy the file:

**Option A — If CLAW.REZ is already on your Windows drive:**

```bash
# Replace the path with where YOUR CLAW.REZ file actually is
cp /mnt/c/path/to/CLAW.REZ ~/projects/OpenClaw/Build_Release/
```

For example, if it's on your Desktop:
```bash
cp "/mnt/c/Users/salman/Desktop/CLAW.REZ" ~/projects/OpenClaw/Build_Release/
```

**Option B — If you need to download it first:**

Download the file on your Windows side (using your browser), then copy it:
```bash
# Example if downloaded to your Downloads folder
cp "/mnt/c/Users/salman/Downloads/CLAW.REZ" ~/projects/OpenClaw/Build_Release/
```

> **Note**: In WSL, Windows drive `C:\` is accessible at `/mnt/c/`. Backslashes become forward slashes.

### 4.3 — Verify CLAW.REZ is in the right place

```bash
ls -lh ~/projects/OpenClaw/Build_Release/CLAW.REZ
```

**Expected output**:
```
-rwxrwxrwx 1 salman salman 170M ... CLAW.REZ
```

The file should be approximately **170 MB**. If it's much smaller, the file might be corrupted or incomplete.

### 4.4 — Create ASSETS.ZIP

OpenClaw also needs an `ASSETS.ZIP` file containing the contents of the `ASSETS` folder:

```bash
cd ~/projects/OpenClaw/Build_Release

# Check if ASSETS folder exists
ls ASSETS/
```

If the `ASSETS` folder exists with files inside, create the zip:

```bash
sudo apt install -y zip
cd ~/projects/OpenClaw/Build_Release
zip -r ASSETS.ZIP ASSETS/
```

**Expected output**:
```
  adding: ASSETS/ (stored 0%)
  adding: ASSETS/somefile... (deflated ...)
  ...
```

### 4.5 — Verify the final directory structure

```bash
ls -lh ~/projects/OpenClaw/Build_Release/
```

**Expected output**: You should see at minimum:
```
ASSETS/
ASSETS.ZIP
CLAW.REZ
config.xml
```

---

## Verify success

```bash
# Check CLAW.REZ exists and has content
file ~/projects/OpenClaw/Build_Release/CLAW.REZ
```

**Expected output**: Something like `data` or a file type description (it's a proprietary archive format).

```bash
# Check ASSETS.ZIP exists
file ~/projects/OpenClaw/Build_Release/ASSETS.ZIP
```

**Expected output**: `Zip archive data, ...`

---

## File summary

| File | What it contains | Source |
|------|------------------|--------|
| `CLAW.REZ` | All game graphics, sounds, music, and level data | Original 1997 Captain Claw game |
| `ASSETS.ZIP` | Additional game assets bundled from the ASSETS folder | Created from source repo |
| `config.xml` | Game configuration (resolution, controls, etc.) | Included in the OpenClaw repo |

---

**Next step**: [05-run-and-play.md](05-run-and-play.md) — Launch the game!
