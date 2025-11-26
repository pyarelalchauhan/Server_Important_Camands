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
