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
scp documentation [https://ss64.com/bash/scp.html]



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
