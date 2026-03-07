# Rsync Quick Reference

## Basic Syntax

```bash
rsync [flags] source destination
```

**Trailing slash matters:**
- `source/` = copy contents of source INTO destination
- `source` = copy the source folder itself INTO destination

```bash
rsync -avz Bhopal/ remote:/dest/Bhopal/    # copies contents into Bhopal/
rsync -avz Bhopal  remote:/dest/            # creates dest/Bhopal/
```

---

## Common Flags

| Flag | Meaning |
|------|---------|
| `-a` | Archive mode (`-rlptgo`): recursive + preserve permissions, timestamps, symlinks, group, owner |
| `-r` | Recursive (descend into directories) |
| `-v` | Verbose (show files being transferred) |
| `-z` | Compress data during transfer (faster over slow networks) |
| `-P` | Show progress + keep partial transfers (`--progress --partial`) |
| `-n` | Dry run (preview without actually copying) |
| `-h` | Human-readable file sizes (KB, MB, GB) |
| `-u` | Skip files that are newer on destination (update only) |
| `--delete` | Delete files on destination that don't exist in source (mirror) |
| `--exclude` | Exclude files/folders matching pattern |
| `--include` | Include files/folders matching pattern (use with exclude) |
| `--max-size` | Skip files larger than specified size |
| `-e` | Specify remote shell (e.g., `-e "ssh -p 2222"`) |
| `-L` | Follow symlinks (copy the file, not the link) |
| `--stats` | Show transfer statistics at the end |
| `--bwlimit` | Limit bandwidth in KB/s |

---

## Most Used Commands

### 1. Copy folder to remote (exclude large dirs)

```bash
rsync -avzP --exclude='outputs/' --exclude='__pycache__/' \
  /DATA/pyare/Accessibility/LiDAR_perception_models/Concerto/Bhopal/ \
  user@remote:/path/to/Bhopal/
```

### 2. Dry run first (see what would transfer)

```bash
rsync -avzn --exclude='outputs/' source/ user@remote:/dest/
```

### 3. Copy with progress bar

```bash
rsync -avzP source/ user@remote:/dest/
```

`-P` shows per-file progress and keeps partially transferred files on interruption.

### 4. Sync (mirror) - delete extra files on destination

```bash
rsync -avz --delete source/ user@remote:/dest/
```

**Warning**: `--delete` removes files on destination that don't exist in source.

### 5. Copy only specific file types

```bash
rsync -avz --include='*.py' --include='*/' --exclude='*' source/ dest/
```

### 6. Exclude multiple patterns

```bash
rsync -avz \
  --exclude='outputs/' \
  --exclude='*.log' \
  --exclude='*.pth' \
  --exclude='__pycache__/' \
  --exclude='.git/' \
  source/ dest/
```

### 7. Limit bandwidth (useful on shared networks)

```bash
rsync -avz --bwlimit=10000 source/ user@remote:/dest/   # 10 MB/s limit
```

### 8. Resume interrupted transfer

```bash
rsync -avzP source/ user@remote:/dest/
```

`-P` (which includes `--partial`) keeps partial files so re-running resumes from where it stopped.

### 9. Copy only newer/changed files

```bash
rsync -avzu source/ user@remote:/dest/
```

`-u` skips files that are already newer on the destination.

### 10. Use non-standard SSH port

```bash
rsync -avz -e "ssh -p 2222" source/ user@remote:/dest/
```

### 11. Local copy (no remote)

```bash
rsync -avh /source/dir/ /backup/dir/
```

Works for local-to-local copies too, faster than `cp -r` for large directories since it skips unchanged files on re-run.

### 12. Skip large files

```bash
rsync -avz --max-size=100m source/ dest/   # skip files > 100MB
```

---

## Common Patterns for This Project

### Transfer configs + scripts (no outputs, no checkpoints)

```bash
rsync -avzP \
  --exclude='outputs/' \
  --exclude='*.pth' \
  --exclude='__pycache__/' \
  /DATA/pyare/Accessibility/LiDAR_perception_models/Concerto/Bhopal/ \
  user@remote:/path/to/Bhopal/
```

### Transfer only model checkpoints

```bash
rsync -avzP --include='*/' --include='*.pth' --exclude='*' \
  /DATA/pyare/Accessibility/LiDAR_perception_models/Concerto/Bhopal/outputs/ \
  user@remote:/path/to/outputs/
```

### Transfer HDF5 dataset with resume support

```bash
rsync -avzP \
  /DATA/pyare/Accessibility/data/Bhopal/bhopal_scene_v9_normals_rgb.h5 \
  user@remote:/path/to/data/
```

### Pull results from remote

```bash
rsync -avzP user@remote:/path/to/outputs/*/logs/*.log ./local_logs/
```
