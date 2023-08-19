# Useful Usage-Related Tools for Linux

## 1. **sysstat**
```bash
apt install sysstat
```
Tools provided:
- `iostat`: Reports CPU statistics and input/output statistics for block devices and partitions.
- `mpstat`: Reports individual or combined processor-related statistics.
- `pidstat`: Reports statistics for Linux tasks (processes) such as I/O, CPU, memory, etc.
- `tapestat`: Reports statistics for tape drives connected to the system.
- `cifsiostat`: Reports CIFS statistics.

## 2. **Monitor Network Traffic**
```bash
sudo apt install nethogs
```
To use:
```bash
nethogs
```

## 3. **Monitor CPU Usage and Temps**
```bash
sudo snap install auto-cpufreq
```
To monitor CPU:
```bash
sudo auto-cpufreq --monitor
```
More info can be found [here](https://github.com/AdnanHodzic/auto-cpufreq).

## 4. **NVMe Information**
```bash
sudo apt install nvme-cli
```
- Get the NVMe lists:
  ```bash
  nvme list
  ```
- Get specific NVMe information:
  ```bash
  sudo nvme smart-log /dev/nvme<id>
  ```

## 5. **Check Disk IO**
```bash
sudo apt-get install iotop
```
To use:
```bash
iotop
```

--- 
