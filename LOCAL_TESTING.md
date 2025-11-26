# Local Testing Guide

This guide explains how to build and test the add-on locally without waiting for GitHub Actions.

## Method 1: Build Locally and Load into Home Assistant

### Step 1: Build the Docker Image

**Windows (PowerShell):**
```powershell
.\build-local.ps1 amd64
```

**Linux/Mac:**
```bash
./build-local.sh amd64
```

This will build the image and tag it as `ghcr.io/eddiekk/amd64-snapcast-server` (or `aarch64` if you specify that architecture).

### Step 2: Export and Load into Home Assistant

**Option A: Using the helper script (recommended)**

**Windows:**
```powershell
# Export only (you'll copy manually)
.\load-to-ha.ps1 amd64

# Or if you have direct access to HA server path
.\load-to-ha.ps1 amd64 "\\ha-server\path\to\copy"
```

**Linux/Mac:**
```bash
# Export only
./load-to-ha.sh amd64

# Or with HA server path
./load-to-ha.sh amd64 "/path/to/ha/server"
```

**Option B: Manual export/import**

1. **Export the image:**
   ```bash
   docker save ghcr.io/eddiekk/amd64-snapcast-server -o snapcast-server-amd64.tar
   ```

2. **Copy to Home Assistant server:**
   - The tar file can be placed **anywhere** on your HA server (it's temporary)
   - Common locations:
     - `/tmp/` (temporary, will be cleaned up)
     - `/config/` (persistent, in your HA config directory)
     - `/share/` (persistent, accessible via Samba)
     - Any location you have access to via SSH
   - Use SCP, SMB share, or any method to copy `snapcast-server-amd64.tar` to your HA server

3. **Load on Home Assistant server:**
   ```bash
   # SSH into your Home Assistant server, then:
   docker load -i /path/to/snapcast-server-amd64.tar
   
   # Example if you put it in /config:
   docker load -i /config/snapcast-server-amd64.tar
   
   # Or if in /tmp:
   docker load -i /tmp/snapcast-server-amd64.tar
   ```

4. **Verify the image is loaded:**
   ```bash
   docker images | grep snapcast-server
   ```

5. **Clean up (optional):**
   After loading, you can delete the tar file - the image is now in Docker's registry:
   ```bash
   rm /path/to/snapcast-server-amd64.tar
   ```

### Step 3: Update config.yaml and Push

**Important:** Make sure the `image` line is **uncommented** in `config.yaml`:

```yaml
image: "ghcr.io/eddiekk/{arch}-snapcast-server"
```

**Note:** The `{arch}` placeholder will be automatically replaced by Home Assistant with your system's architecture (amd64 or aarch64).

**Push to GitHub:**
```bash
git add snapcast-server/config.yaml
git commit -m "Update config for local image testing"
git push origin main
```

Wait a minute for GitHub to update before proceeding.

### Step 4: Refresh Repository and Install in Home Assistant

1. In Home Assistant, go to **Settings → Add-ons → Add-on Store**
2. Click the three dots (⋮) → **Repositories**
3. Refresh your repository or remove and re-add it: `https://github.com/eddiekk/hassio-snapcast`
4. Find "Snapcast server" and install it
5. The add-on will use your locally loaded image instead of building

**Important Notes:**
- The tar file location doesn't matter - once loaded with `docker load`, the image is in Docker's registry
- You can delete the tar file after loading (it's no longer needed)
- If you update the image, you'll need to load the new tar file again
- Make sure you load the correct architecture (amd64 for x86_64 systems, aarch64 for ARM64)

---

## Method 2: Force Local Build in Home Assistant (Easier)

This method lets Home Assistant build the add-on itself, which is simpler but takes longer.

### Step 1: Comment out the image line

In `snapcast-server/config.yaml`, comment out the image line:

```yaml
# Comment out image line to force local build in Home Assistant
# image: "ghcr.io/eddiekk/{arch}-snapcast-server"
```

### Step 2: Push to GitHub

**IMPORTANT:** You must push this change to the `main` branch for Home Assistant to see it:

```bash
git add snapcast-server/config.yaml
git commit -m "Comment out image line for local testing"
git push origin main
```

Wait a minute or two for GitHub to update, then proceed to the next step.

### Step 3: Refresh Repository and Install in Home Assistant

1. In Home Assistant, go to **Settings → Add-ons → Add-on Store**
2. Click the three dots (⋮) → **Repositories**
3. Find your repository and click the refresh icon, or remove and re-add it: `https://github.com/eddiekk/hassio-snapcast`
4. Install or update the add-on
5. Home Assistant will build it locally (takes 10-30 minutes)

### Step 4: Remember to Uncomment Before Production

**Important:** Before pushing to GitHub for production, uncomment the image line so GitHub Actions can publish the image to the registry.

---

## Quick Reference

| Method | Speed | Complexity | Best For |
|--------|-------|------------|----------|
| Local Docker Build + Load | Fast (after first build) | Medium | Quick iteration, testing |
| HA Local Build | Slow (10-30 min) | Easy | One-time testing, no Docker needed |

## Troubleshooting

### Builder image pull denied / manifest unknown

**Error:** `docker: Error response from daemon: manifest unknown` or `denied`

**Solution:**
The Home Assistant builder images are **architecture-specific**. Use the correct image for your architecture:

1. **For amd64:**
   ```powershell
   docker pull ghcr.io/home-assistant/amd64-builder:latest
   ```

2. **For aarch64:**
   ```powershell
   docker pull ghcr.io/home-assistant/aarch64-builder:latest
   ```

3. **If authentication is required:**
   ```powershell
   # Create a GitHub Personal Access Token at:
   # https://github.com/settings/tokens
   # Scope needed: read:packages
   
   docker login ghcr.io -u YOUR_GITHUB_USERNAME -p YOUR_TOKEN
   ```

4. **Check available versions:**
   - amd64: https://github.com/home-assistant/builder/pkgs/container/amd64-builder
   - aarch64: https://github.com/home-assistant/builder/pkgs/container/aarch64-builder

**Note:** The build scripts automatically use the correct architecture-specific builder image.

### Docker connection error on Windows
**Error:** `docker: error during connect: ... The system cannot find the file specified`

**Solution:**
1. **Start Docker Desktop** - Make sure Docker Desktop is running (check for the whale icon in the system tray)
2. **Wait for full startup** - Docker Desktop can take 30-60 seconds to fully start
3. **Run as Administrator** - If Docker Desktop is running but you still get the error, try running PowerShell as Administrator
4. **Restart Docker Desktop** - Sometimes a restart fixes connectivity issues

**Verify Docker is working:**
```powershell
docker ps
```
If this command works, Docker is running correctly.

### Image not found after loading
- Make sure you loaded the correct architecture (amd64 vs aarch64)
- Check: `docker images | grep snapcast-server`
- Verify the image name matches what's in `config.yaml`

### Build fails
- Make sure Docker is running (see above)
- Check you have enough disk space (builds can be several GB)
- Verify you're using the correct architecture for your system
- On Windows, ensure Docker Desktop is using WSL 2 backend (Settings → General → Use WSL 2 based engine)

### Home Assistant can't find the image
- Ensure the `image` line in `config.yaml` matches the loaded image name
- The image name format is: `ghcr.io/eddiekk/{arch}-snapcast-server`
- Restart the Home Assistant Supervisor after loading the image

