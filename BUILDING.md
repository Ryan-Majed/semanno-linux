# Building Semanno Linux

This guide provides comprehensive instructions for building the Semanno Linux ISO from source.

## Table of Contents

- [Prerequisites](#prerequisites)
- [Build Environment Setup](#build-environment-setup)
- [Building the ISO](#building-the-iso)
- [Advanced Configuration](#advanced-configuration)
- [Troubleshooting](#troubleshooting)

## Prerequisites

### Required Software

```bash
pacman -S archiso mkinitcpio
```

### System Requirements

- **Arch Linux** or compatible derivative (Manjaro, EndeavourOS, etc.)
- **Disk Space:** 20GB free (for build artifacts)
- **RAM:** 4GB minimum (8GB recommended)
- **CPU:** Any x86-64 processor
- **Internet:** Required for downloading packages

### Permissions

The build script requires `sudo` access to:
- Mount filesystems
- Create device nodes
- Manage package installation

## Build Environment Setup

### 1. Clone the Repository

```bash
git clone https://github.com/Ryan-Majed/semanno-linux.git
cd semanno-linux
git checkout arch-xfce-setup
```

### 2. Verify Build Prerequisites

```bash
# Check if archiso is installed
which mkarchiso
# Output should be: /usr/bin/mkarchiso

# Check if mkinitcpio is available
which mkinitcpio
# Output should be: /usr/sbin/mkinitcpio
```

### 3. Set Permissions on Build Scripts

```bash
chmod +x scripts/build-iso.sh
chmod +x scripts/setup-xfce.sh
```

## Building the ISO

### Quick Build

```bash
sudo ./scripts/build-iso.sh
```

The script will:
1. Check dependencies
2. Verify disk space (20GB minimum)
3. Verify internet connection
4. Create build directories
5. Compile the ISO
6. Generate SHA256 checksums
7. Output results to `out/`

### Build Output

Upon successful completion:

```
✓ Build completed successfully!
✓ ISO: out/semanno-linux-2025.1-x86_64.iso
✓ SHA256: out/SHA256SUMS
✓ MD5: out/MD5SUMS

Total build time: ~15-45 minutes (depends on internet speed)
```

### Build Phases Explained

#### Phase 1: Initialization
- Checks for required tools
- Verifies disk space
- Creates temporary build directories

#### Phase 2: Package Preparation
- Downloads package list
- Verifies package integrity
- Prepares pacman database

#### Phase 3: ISO Compilation
- Creates root filesystem
- Installs base system
- Configures boot loaders (BIOS & UEFI)
- Adds Xfce desktop environment
- Installs live utilities

#### Phase 4: Finalisation
- Generates ISO filesystem
- Creates bootable ISO
- Generates checksums
- Cleans temporary files

## Advanced Configuration

### Custom ISO Name and Version

Edit `scripts/build-iso.sh`:

```bash
# Line ~20
iso_label="semanno"
iso_version="2025.1"
iso_name="semanno-linux"
```

### Adding Custom Packages

Edit `packages/packages.x86_64`:

```bash
# Add one package per line
firefox
git
vim
your-custom-package
```

### Using Custom Pacman Mirrors

Edit `configs/pacman.conf`:

```bash
[core]
Server = https://mirror.example.com/archlinux/$repo/os/$arch
```

### Modifying Initramfs

Edit `configs/mkinitcpio.conf`:

```bash
# Add modules, binaries, or hooks as needed
HOOKS="base udev ... your-hooks"
```

### Customising Boot Loader

Configuration is in:
- BIOS: `airootfs/boot/syslinux/syslinux.cfg`
- UEFI: `efiboot/loader/loader.conf`

## Troubleshooting

### "Permission Denied" Error

**Error Message:**
```
mkarchiso: error: root user required
```

**Solution:**
```bash
sudo ./scripts/build-iso.sh
# Always run with sudo
```

### "mkarchiso: command not found"

**Error Message:**
```
mkarchiso: command not found
```

**Solution:**
```bash
sudo pacman -S archiso
```

### "Insufficient disk space" Error

**Error Message:**
```
Error: Not enough disk space (requires 20GB, available: XGB)
```

**Solutions:**
1. Free up disk space:
```bash
sudo pacman -Sc  # Clear pacman cache
```

2. Use external storage:
```bash
cd /mnt/external-drive/
~/semanno-linux/scripts/build-iso.sh
```

3. Reduce package count:
```bash
# Edit packages/packages.x86_64 and remove unnecessary packages
```

### Network Connection Failed

**Error Message:**
```
Error: Could not download packages (check internet connection)
```

**Solutions:**
1. Check your connection:
```bash
ping archlinux.org
```

2. Change Pacman mirror:
```bash
sudo pacman-mirrors --fasttrack
```

3. Manually specify mirror in `configs/pacman.conf`

### Build Hangs or Takes Too Long

**Symptoms:**
- Build seems stuck for >1 hour
- High disk I/O but little progress

**Solutions:**
1. Check system resources:
```bash
htop  # Monitor CPU, RAM, I/O
```

2. Check disk speed:
```bash
sudo hdparm -t /dev/sdX  # Test read speed
```

3. Kill and retry:
```bash
sudo killall mkarchiso
sudo rm -rf build/ work/
sudo ./scripts/build-iso.sh
```

### ISO Won't Boot

**Symptoms:**
- USB drive not recognized
- Hangs at boot
- "ISOLINUX not found" error

**Solutions:**
1. Verify checksum:
```bash
sha256sum -c out/SHA256SUMS
```

2. Re-flash USB with correct tool:
```bash
# Using dd
sudo dd if=out/semanno-linux-2025.1-x86_64.iso of=/dev/sdX bs=4M conv=fsync

# Using Etcher (GUI)
# Download from balena.io/etcher
balena-etcher-x64.zip
```

3. Try UEFI vs BIOS mode in BIOS settings

4. Try different USB ports

5. Try different USB drive

### Package Installation Fails During Build

**Error Message:**
```
error: could not prepare transaction
error: failed to commit transaction
```

**Solutions:**
1. Check `packages/packages.x86_64` for typos:
```bash
pacman -S nonexistent-package  # Test package names first
```

2. Remove conflicting packages:
```bash
# Edit packages/packages.x86_64
# Remove conflicting entries
```

3. Clear pacman cache and rebuild:
```bash
sudo pacman -Sc
sudo ./scripts/build-iso.sh
```

### Xfce Desktop Won't Start

**Symptoms:**
- Black screen after boot
- Login screen appears but no desktop after login

**Solutions:**
1. During installation, verify Xfce is selected in archinstall
2. After boot, run post-install script:
```bash
sudo ./scripts/setup-xfce.sh
```

3. Check LightDM status:
```bash
systemctl status lightdm
```

### Audio Not Working

**Symptoms:**
- No sound output
- Volume controls missing

**Solutions:**
1. Run setup script:
```bash
sudo ./scripts/setup-xfce.sh
```

2. Check Pipewire status:
```bash
systemctl --user status pipewire
```

3. Test audio:
```bash
speaker-test -c2 -l1 -twav
```

### Network Not Available in Live Environment

**Symptoms:**
- No WiFi detected
- Ethernet not working

**Solutions:**
1. Enable NetworkManager:
```bash
systemctl start NetworkManager
nmtui  # Network configuration tool
```

2. Check drivers:
```bash
lspci | grep -i network
lsmod | grep -i wireless
```

3. Load missing modules:
```bash
sudo modprobe -a your-network-module
```

### Out of Memory During Build

**Error Message:**
```
error: could not allocate memory
```

**Solutions:**
1. Close unnecessary applications
2. Disable swap compression to free RAM:
```bash
swapoff -a
```

3. Use a system with more RAM
4. Build during off-peak hours when system is less loaded

### Build Completes but ISO Won't Open

**Error Message:**
```
corrupted or not an ISO 9660 image
```

**Solutions:**
1. Verify ISO was fully written:
```bash
ls -lh out/semanno-linux-2025.1-x86_64.iso
```

2. Check for corruption:
```bash
sha256sum out/semanno-linux-2025.1-x86_64.iso
```

3. Mount and verify contents:
```bash
sudo mount -o loop out/semanno-linux-2025.1-x86_64.iso /mnt/iso
ls -la /mnt/iso/
```

## Getting Help

- Check Arch Linux Wiki: https://wiki.archlinux.org
- Arch Linux Forums: https://bbs.archlinux.org
- This project's Issues: https://github.com/Ryan-Majed/semanno-linux/issues

## Performance Optimization

### Faster Builds

1. **Use fast mirrors:**
```bash
sudo pacman-mirrors --fasttrack
```

2. **Use SSD for build:**
```bash
cd /mnt/nvme-drive/
~/semanno-linux/scripts/build-iso.sh
```

3. **Parallel downloads:**
```bash
# Edit /etc/pacman.conf
ParallelDownloads = 10
```

### Smaller ISO

1. **Remove optional packages** from `packages/packages.x86_64`
2. **Remove language packs** (keep only needed languages)
3. **Disable debug symbols:**
```bash
# Edit configs/pacman.conf
CFLAGS="-O2 -pipe"
```

---

**Happy building! 🚀**
