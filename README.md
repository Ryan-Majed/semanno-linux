# Semanno Linux

**A lightweight, rolling-release Arch-based Linux distribution featuring Xfce desktop environment, pre-configured with archinstall and all standard live utilities.**

## Features

- 🐧 **Arch-Based** - Access to cutting-edge packages and AUR
- 🔄 **Rolling Release** - Continuous updates, never a version number
- 🎨 **Xfce Desktop** - Lightweight yet feature-rich desktop environment
- 📦 **Pre-configured** - archinstall and all standard utilities included
- ⚡ **Live Environment** - Bootable ISO with complete desktop experience
- 🔧 **Fully Automated** - Build scripts for easy ISO generation
- 🎯 **Minimal Bloat** - Only essential packages included

## System Requirements

### Build Requirements
- Arch Linux (or compatible distribution)
- `mkarchiso` installed
- 20GB free disk space for build process
- 4GB RAM minimum
- Root/sudo access

### Runtime Requirements
- 64-bit x86 processor
- 2GB RAM minimum (4GB+ recommended)
- 20GB storage for installation
- UEFI or BIOS boot support

## Directory Structure

```
semanno-linux/
├── configs/
│   ├── pacman.conf          # Package manager configuration
│   └── mkinitcpio.conf      # Initramfs generation settings
├── packages/
│   └── packages.x86_64      # Complete package list
├── scripts/
│   ├── build-iso.sh         # Main ISO build script
│   └── setup-xfce.sh        # Post-install Xfce configuration
├── README.md                # This file
├── BUILDING.md              # Detailed build instructions
└── .gitignore
```

## Quick Start

### Building the ISO

```bash
sudo ./scripts/build-iso.sh
```

The ISO will be generated in the `out/` directory.

### Burning to USB

```bash
sudo dd if=out/semanno-linux-2025.1-x86_64.iso of=/dev/sdX bs=4M conv=fsync
# Replace sdX with your USB device name
```

### Booting and Installation

1. Insert USB and boot
2. Select "Install" or run `archinstall` in the live environment
3. Follow the installer prompts
4. Post-install, run: `sudo ./scripts/setup-xfce.sh`

## Included Tools

### Live Environment
- `archinstall` - Guided Arch installation tool
- `fdisk`, `parted` - Disk partitioning
- `lsblk`, `blkid` - Disk identification
- File managers and compression tools

### Development
- `git`, `vim`, `base-devel`
- Build tools and headers
- Python, Perl interpreters

### System Utilities
- `htop`, `neofetch`, `inxi` - System info
- NetworkManager - Network configuration
- Pipewire - Modern audio system
- Bluetooth and CUPS support

### Desktop Utilities
- Firefox web browser
- Gedit text editor
- PCManFM file manager
- Archive manager (file-roller)

## Configuration

### Custom Packages

Edit `packages/packages.x86_64` to add/remove packages:

```bash
# Add your custom package
echo "custom-package" >> packages/packages.x86_64
```

### Pacman Configuration

Modify `configs/pacman.conf` for custom repositories or mirrors.

### Build Settings

Edit `scripts/build-iso.sh` to customize:
- ISO name and version
- Volume label
- Build directories

## Troubleshooting

### Build fails due to missing dependencies

```bash
pacman -S archiso mkinitcpio
```

### Permission denied errors

Ensure the script is run with `sudo`:

```bash
sudo ./scripts/build-iso.sh
```

### ISO too large for USB

Reduce packages in `packages/packages.x86_64` or use a larger USB drive (8GB minimum).

### Boot issues

- Verify USB with: `sha256sum -c out/SHA256SUMS`
- Try both UEFI and BIOS boot modes
- Check BIOS boot order settings

## Support

For issues, bugs, or feature requests, please open an issue on GitHub.

For Arch-specific help: https://wiki.archlinux.org

## License

This project is provided as-is. Arch Linux and all included packages retain their original licenses.

---

**Semanno Linux - Built by the community, for the community** 🚀
