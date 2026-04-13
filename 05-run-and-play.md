# Step 5: Run and Play OpenClaw

## What you'll do

Launch the OpenClaw game and start playing Captain Claw!

---

## Prerequisites

- Completed [Step 3: Clone and Build](03-clone-and-build.md) (game is compiled)
- Completed [Step 4: Game Files](04-game-files.md) (CLAW.REZ is in place)
- Display is working (verified in [Step 2](02-dependencies.md), section 2.6)

---

## Steps

### 5.1 — Make sure your display is working

Before launching the game, verify you have a display connection:

**If using WSLg (Windows 11 / recent Windows Server 2022)**:
```bash
# Test with a simple app
xclock &
```
A small clock window should appear on your Windows desktop. Close it after confirming.

**If using VcXsrv**: Make sure XLaunch is running on the Windows side (see [Step 2, section 2.6](02-dependencies.md)).

### 5.2 — Navigate to the Build_Release directory

```bash
cd ~/projects/OpenClaw/Build_Release
```

### 5.3 — Copy the compiled binary to Build_Release

The compiled `openclaw` binary is in the `build` folder. Copy it to where the game files are:

```bash
cp ~/projects/OpenClaw/build/openclaw .
```

### 5.4 — Launch OpenClaw

```bash
./openclaw
```

**Expected result**: The game window should open! You'll see the Captain Claw title screen.

> **If nothing happens or you get an error**, see the [Troubleshooting guide](06-troubleshooting.md).

### 5.5 — (Optional) Start background music

If you installed timidity in Step 2, the MIDI music should play automatically. If you hear no music:

```bash
# Start timidity as a background MIDI server
timidity -iA &

# Then launch the game
./openclaw
```

---

## Game Controls

| Key | Action |
|-----|--------|
| Arrow keys | Move left / right / climb |
| Z | Attack (sword) |
| X | Jump |
| Space | Pistol / ranged attack |
| Escape | Pause / Menu |
| F5 | Quick save |
| F9 | Quick load |
| Alt + Enter | Toggle fullscreen |

> **Note**: Controls may vary. Check `config.xml` in the Build_Release folder to view or change key bindings.

---

## Running the game in the future

Every time you want to play, you just need two things:

1. **Open Ubuntu** from the Start menu
2. Run:
```bash
cd ~/projects/OpenClaw/Build_Release
./openclaw
```

That's it!

### (Optional) Create a shortcut command

Add this to your `~/.bashrc` so you can type `openclaw` from anywhere:

```bash
echo 'alias openclaw="cd ~/projects/OpenClaw/Build_Release && ./openclaw"' >> ~/.bashrc
source ~/.bashrc
```

Now you can just type:
```bash
openclaw
```

---

## Verify success

If the game launches and you see the title screen, congratulations — you've successfully set up OpenClaw!

If something went wrong, check the [Troubleshooting guide](06-troubleshooting.md).

---

**Having problems?** [06-troubleshooting.md](06-troubleshooting.md) — Common errors and fixes
