## Useful Server Cammands
```bash
# Monitor the free gpu
watch -n 30 'nvidia-smi --query-gpu=index,memory.free --format=csv'
```


```bash
 # Find the process
  pgrep -a -f "tools/train.py.*ptv3_hdf5_140gb_12class"

  # Kill by pattern (kills all matching processes)
  pkill -9 -f "tools/train.py.*ptv3_hdf5_140gb_12class"

  # Or kill specific PID
  kill -9 <PID>

  # Method 3: Check and kill all Python training processes

  # Find all training processes
  ps aux | grep "train.py" | grep -v grep

  # Kill all of them
  pkill -9 -f "train.py"
```
- (scp documentation)[https://ss64.com/bash/scp.html]



```bash
 # Total CPU cores available
  nproc

  # Detailed CPU info
  lscpu | grep -E "^CPU\(s\)|^Thread|^Core|^Socket"

  # Current CPU usage and load
  htop  # Interactive (press q to quit)
  # OR
  top -bn1 | head -20

  # Load average (1min, 5min, 15min)
  uptime

  # See running processes by CPU usage
  ps aux --sort=-%cpu | head -15

  # Check memory usage too
  free -h

  Quick summary command:
  echo "CPU Cores: $(nproc)" && echo "Load: $(uptime | awk -F'load average:' '{print $2}')" && free -h | grep Mem

  For num_workers in training:
  - Rule of thumb: num_workers = min(nproc, 4 * num_gpus)
  - Current config uses num_worker = 16
  - If load is high, reduce to 8 or 12
```

```bash
nvidia-smi --query-gpu=utilization.gpu,utilization.memory,memory.used,memory.total --format=csv -l 1
```
### When nvml or cuda driver mismatch 
1. First, fix any broken packages:
```bash
sudo apt --fix-broken install
```
 2. Then reboot your system:
```bash
sudo reboot
```
3. If reboot is not possible right now:

  You can try unloading and reloading the NVIDIA kernel modules (risky if you have a GUI running):
  ```bash
sudo rmmod nvidia_uvm nvidia_drm nvidia_modeset nvidia
sudo modprobe nvidia
```
```bash
nvidia-smi --query-gpu=index,name,memory.used,memory.total,memory.free,utilization.gpu,temperature.gpu --format=csv
```
```bash
watch -n 2 'nvidia-smi --query-gpu=memory.used,memory.total,utilization.gpu --format=csv,noheader | head -1 && echo
   "---" && nvidia-smi --query-compute-apps=pid,used_memory --format=csv,noheader 2>/dev/null | head -5'
```
```bash
Push to agrihub (local → remote):
  rsync -avz --progress /NFSDISK2/pyare/LiDAR-Subsampling-Benchmark/PTv3/
  agrihub:/home/vaibhavk/vaibhav/pyare/LiDAR-Subsampling-Benchmark/PTv3/

  Pull from agrihub (remote → local):
  rsync -avz --progress agrihub:/home/vaibhavk/vaibhav/pyare/LiDAR-Subsampling-Benchmark/PTv3/
  /NFSDISK2/pyare/LiDAR-Subsampling-Benchmark/PTv3/

  Dry run (preview changes without syncing):
  # Add -n flag for dry run
  rsync -avzn --progress /NFSDISK2/pyare/LiDAR-Subsampling-Benchmark/PTv3/
  agrihub:/home/vaibhavk/vaibhav/pyare/LiDAR-Subsampling-Benchmark/PTv3/

  Sync only configs and scripts (exclude large files):
  rsync -avz --progress \
    --include='configs/***' \
    --include='scripts/***' \
    --include='SemanticKITTI/***' \
    --exclude='outputs/***' \
    --exclude='*.pth' \
    --exclude='__pycache__/' \
    /NFSDISK2/pyare/LiDAR-Subsampling-Benchmark/PTv3/ \
    agrihub:/home/vaibhavk/vaibhav/pyare/LiDAR-Subsampling-Benchmark/PTv3/

  Flags explanation:
  - -a: Archive mode (preserves permissions, timestamps, symlinks)
  - -v: Verbose output
  - -z: Compress during transfer
  - --progress: Show transfer progress
  - -n: Dry run (test without making changes)
  - --delete: Remove files on destination that don't exist on source (use with caution)
```
