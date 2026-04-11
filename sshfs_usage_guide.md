# SSHFS Usage Guide

> Mount remote directories over SSH as local folders — no file copying needed.

---

## What is SSHFS?

SSHFS (SSH Filesystem) uses FUSE to mount a remote directory on your local machine via SSH. Once mounted, the remote path behaves like any local folder — you can browse, read, and write files transparently.

---

## Installation

```bash
# Ubuntu / Debian
sudo apt install sshfs

# Fedora / RHEL
sudo dnf install fuse-sshfs

# macOS (via Homebrew + macFUSE)
brew install macfuse sshfs
```

> Only needs to be installed once system-wide. All users (including non-sudo) can use it after installation.

---

## Basic Usage

### Mount a Remote Directory

```bash
sshfs user@remotehost:/remote/path /local/mountpoint
```

**Example:**
```bash
mkdir -p ~/remote_data
sshfs pyare@geoai2:/data/BhopalMLS ~/remote_data
```

### Unmount

```bash
fusermount -u ~/remote_data        # Linux
umount ~/remote_data               # macOS
```

---

## Common Options

| Option | Description |
|--------|-------------|
| `-p 2222` | Use a custom SSH port |
| `-o reconnect` | Auto-reconnect on disconnect |
| `-o follow_symlinks` | Follow symlinks on the remote |
| `-o cache=yes` | Enable caching (faster reads) |
| `-o Compression=yes` | Enable SSH compression (good for slow networks) |
| `-o IdentityFile=~/.ssh/id_rsa` | Specify SSH key |
| `-o allow_other` | Allow other users to access the mount |

**Example with options:**
```bash
sshfs user@geoai2:/data ~/remote_data \
  -o reconnect \
  -o follow_symlinks \
  -o IdentityFile=~/.ssh/id_ed25519
```

---

## Persistent Mount (fstab)

To auto-mount on boot, add to `/etc/fstab`:

```
user@remotehost:/remote/path  /local/mountpoint  fuse.sshfs  defaults,_netdev,reconnect,uid=1000,gid=1000  0  0
```

> Replace `uid` and `gid` with your user's values (`id -u` and `id -g`).

---

## SSH Key Setup (Recommended)

Password prompts break automated or persistent mounts. Set up key-based auth:

```bash
# Generate a key (if you don't have one)
ssh-keygen -t ed25519

# Copy key to remote server
ssh-copy-id user@remotehost

# Now sshfs won't ask for a password
sshfs user@remotehost:/path ~/mountpoint
```

---

## Lab Server Examples (GeoAI4Cities)

```bash
# Mount BhopalMLS dataset from geoai2
mkdir -p ~/mnt/bhopal
sshfs pyare@geoai2:/data/BhopalMLS ~/mnt/bhopal -o reconnect

# Mount shared lab storage
mkdir -p ~/mnt/labshare
sshfs vaibhav_iiserb@geoai2:/mnt/shared ~/mnt/labshare -o reconnect,allow_other

# Unmount all
fusermount -u ~/mnt/bhopal
fusermount -u ~/mnt/labshare
```

---

## Tips & Best Practices

- **Don't use for training I/O** — sshfs adds network latency. Keep training data local on the server's NVMe. Use sshfs for browsing, editing configs, and managing files.
- **Check mount status:** `df -h` or `mount | grep sshfs`
- **If mount hangs:** Use `fusermount -uz ~/mountpoint` (lazy unmount) to force it.
- **VS Code remote** — For heavy editing, prefer VS Code's Remote-SSH extension over sshfs. Use sshfs when you need file manager / GUI tool access to remote files.
- **Permissions** — Files appear with the remote user's permissions. Use `-o uid=$(id -u)` to remap ownership locally if needed.

---

## Troubleshooting

| Problem | Fix |
|---------|-----|
| `Transport endpoint is not connected` | `fusermount -uz /mountpoint` then remount |
| `Permission denied` | Check SSH key is authorized on remote; check remote directory permissions |
| `fuse: bad mount point` | Ensure local mountpoint directory exists (`mkdir -p /mountpoint`) |
| Slow performance | Add `-o cache=yes,Compression=yes` or switch to rsync for bulk transfers |
| Mount lost after network drop | Add `-o reconnect,ServerAliveInterval=15,ServerAliveCountMax=3` |

---

## Quick Reference

```bash
# Mount
sshfs user@host:/remote/path /local/path

# Mount with reconnect + key
sshfs user@host:/path /local/path -o reconnect,IdentityFile=~/.ssh/id_ed25519

# Check mounts
mount | grep sshfs

# Unmount
fusermount -u /local/path

# Force unmount (if stuck)
fusermount -uz /local/path
```
