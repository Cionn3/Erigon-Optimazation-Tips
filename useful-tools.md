# Useful usage related tools for Linux

1. sysstat

apt install sysstat

- iostat reports CPU statistics and input/output statistics for block devices and partitions.
- mpstat reports individual or combined processor related statistics.
- pidstat reports statistics for Linux tasks (processes) : I/O, CPU, memory, etc.
- tapestat reports statistics for tape drives connected to the system.
- cifsiostat reports CIFS statistics.

2. Monitor Network Traffic

sudo aptinstall nethogs

to use it:
nethogs

3. Monitor CPU Usage and Temps

sudo snap install auto-cpufreq

to monitor cpu:
sudo auto-cpufreq --monitor

more info: https://github.com/AdnanHodzic/auto-cpufreq


4. Nvme Information

sudo apt install nvme-cli

Get the nvme lists

nvme list

Get the nvme information

sudo nvme smart-log /dev/nvme<id>

5. Check Disk IO

sudo apt-get install iotop

use it
iotop