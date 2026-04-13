# Step 6: Troubleshooting

## Common errors and how to fix them

---

### Error: "cannot open display" or "No display available"

**What it means**: The game can't find a screen to draw on.

**Fix for WSLg** (Windows 11 / recent Windows Server 2022):
```bash
# Check if WSLg is working
echo $DISPLAY
```
It should show something like `:0` or `:0.0`. If empty:
```bash
export DISPLAY=:0
```

**Fix for VcXsrv** (older Windows):
1. Make sure **XLaunch** is running on your Windows desktop
2. Set the display variable:
```bash
export DISPLAY=$(cat /etc/resolv.conf | grep nameserver | awk '{print $2}'):0
echo $DISPLAY
```
3. You should see an IP address followed by `:0`

---

### Error: "CLAW.REZ not found" or "Failed to load resources"

**What it means**: The game can't find the original game files.

**Fix**:
```bash
# Make sure you're in the right directory
cd ~/projects/OpenClaw/Build_Release

# Check the file exists
ls -la CLAW.REZ
```

If the file is missing, go back to [Step 4](04-game-files.md).

If the file exists but the game still can't find it, try:
```bash
# Run from the correct directory
cd ~/projects/OpenClaw/Build_Release
./openclaw
```

The game must be launched from the `Build_Release` directory.

---

### Error: "libSDL2 not found" or missing .so files

**What it means**: SDL2 libraries aren't installed or can't be found.

**Fix**:
```bash
# Reinstall all SDL2 libraries
sudo apt install -y libsdl2-dev libsdl2-image-dev libsdl2-mixer-dev libsdl2-ttf-dev libsdl2-gfx-dev
```

If that doesn't help:
```bash
# Check if the libraries are present
ldconfig -p | grep SDL2
```
You should see multiple lines with `libSDL2`. If not, try:
```bash
sudo ldconfig
```

---

### Error: CMake fails with "Could not find SDL2"

**What it means**: CMake can't locate the SDL2 development files.

**Fix**:
```bash
# Make sure dev packages are installed (not just runtime packages)
sudo apt install -y libsdl2-dev

# Clean the build folder and try again
cd ~/projects/OpenClaw
rm -rf build
mkdir build
cd build
cmake ..
```

---

### Error: "make" fails with compilation errors

**Possible fixes**:

1. **Missing dependency**:
```bash
sudo apt install -y build-essential cmake libsdl2-dev libsdl2-image-dev libsdl2-mixer-dev libsdl2-ttf-dev libsdl2-gfx-dev libtinyxml-dev
```

2. **Out of memory** (common with WSL):
```bash
# Use fewer parallel jobs
make -j2
# Or single-threaded if still failing
make
```

3. **Clean build** (start fresh):
```bash
cd ~/projects/OpenClaw
rm -rf build
mkdir build
cd build
cmake ..
make -j$(nproc)
```

---

### No background music

**What it means**: MIDI playback isn't configured.

**Fix**:
```bash
# Install MIDI support
sudo apt install -y timidity freepats

# Start timidity as a MIDI server
timidity -iA &

# Then launch the game
./openclaw
```

---

### Game runs but window is tiny / wrong resolution

**Fix**: Edit the config file:
```bash
nano ~/projects/OpenClaw/Build_Release/config.xml
```

Look for resolution settings and change them. For example:
```xml
<Display>
    <Resolution width="1920" height="1080"/>
    <Fullscreen>false</Fullscreen>
</Display>
```

Save with **Ctrl+O**, then **Enter**, then exit with **Ctrl+X**.

---

### Game is very slow / low FPS

**Possible causes and fixes**:

1. **GPU acceleration not available in WSL** — This is a known limitation. Try:
   ```bash
   export LIBGL_ALWAYS_SOFTWARE=1
   ./openclaw
   ```

2. **VcXsrv performance** — If using VcXsrv, make sure you selected **"Native opengl"** option when launching XLaunch.

3. **WSL memory limit** — Create or edit `.wslconfig` on Windows:
   Open PowerShell and run:
   ```powershell
   notepad $env:USERPROFILE\.wslconfig
   ```
   Add:
   ```ini
   [wsl2]
   memory=4GB
   processors=4
   ```
   Then restart WSL:
   ```powershell
   wsl --shutdown
   ```

---

### WSL won't start / "The WSL 2 kernel file is not found"

**Fix**:
```powershell
# Run in PowerShell as Administrator
wsl --update
```

If that doesn't work:
```powershell
# Force update
wsl --update --web-download
```

Then restart your computer.

---

### "Permission denied" when running ./openclaw

**Fix**:
```bash
chmod +x ~/projects/OpenClaw/Build_Release/openclaw
```

---

### Audio crackling or distortion

**Fix**: Try different audio settings:
```bash
export SDL_AUDIODRIVER=pulseaudio
./openclaw
```

Or:
```bash
export SDL_AUDIODRIVER=alsa
./openclaw
```

---

## Nuclear option: Start completely fresh

If nothing works and you want to start over:

```bash
# Remove everything
rm -rf ~/projects/OpenClaw

# Then go back to Step 3 and start again
```

And revisit each step:
1. [01-wsl-setup.md](01-wsl-setup.md)
2. [02-dependencies.md](02-dependencies.md)
3. [03-clone-and-build.md](03-clone-and-build.md)
4. [04-game-files.md](04-game-files.md)
5. [05-run-and-play.md](05-run-and-play.md)

---

## Still stuck?

- **OpenClaw GitHub Issues**: https://github.com/pjasicek/OpenClaw/issues — Search for your error or ask the community
- **WSL Documentation**: https://learn.microsoft.com/en-us/windows/wsl/ — Official Microsoft WSL docs
