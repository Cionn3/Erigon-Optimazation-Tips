# Linux Optimizations

Even if you have a fast NVMe setup, doing some page cache optimizations might give you a little boost.

## 1. File System

Make sure you are using `ext4` or `xfs` as they are most commonly used, and their performance on NVMe drives is well-documented.

## 2. IO Scheduler

Check your NVMe scheduler. If it's already set to `none`, use this command:

```
cat /sys/block/nvme<id>/queue/scheduler
```

If you don't know your NVMe ID, you can use a tool like `iostat` to see the IDs.

## 3. Install IRQ Balance

If you haven't already, you can install it:

```
sudo apt install irqbalance
systemctl start irqbalance
systemctl status irqbalance
```

More info can be found [here](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/6/html/performance_tuning_guide/sect-red_hat_enterprise_linux-performance_tuning_guide-tool_reference-irqbalance).

## 4. Disabling noatime and setting commit=60

If you have powerful NVMes, this probably won't make much difference, but it's good to have. 

Edit the `/etc/fstab` and find your disk name, then include the `noatime,commit=60`. If you're using RAID 0, it will probably be something like `md0`.

**Example config**:

```
# /etc/fstab: static file system information.
# Use 'blkid' to print the universally unique identifier for a device.
...
UUID=c74d59d... /               ext4    errors=remount-ro,noatime,commit=60 0       1
...
```

For changes to take effect, you should remount or reboot.

Check if the changes have been applied:

```
cat /proc/mounts | grep ' / '
```

## 5. Enabling Fast Commit

If you're on kernel 5.10 or later, you can enable the fast commit feature. Ubuntu 22.04 already uses 5.15+, but you can always check your kernel version:

```
uname -r
```

If your version supports it, enable it with:

```
sudo tune2fs -O fast_commit /dev/<mountid>
```

And see if the changes have been applied:

```
sudo tune2fs -l /dev/<mountid> | grep features
```

---

## Page Cache Optimizations

### Setting vfs cache values

You can save these settings in your `etc/sysctl.conf` to make them permanent.

**Speed Optimizations**:

```
vm.swappiness=0
vm.vfs_cache_pressure=30
vm.dirty_background_ratio=20
vm.dirty_ratio=35
vm.dirty_expire_centisecs=6000
vm.dirty_writeback_centisecs=500
```

For these changes to be applied, run:

```
sudo sysctl -p
```

It's always a good idea to reboot afterward. Adjust these values according to your hardware.

Source: [Baeldung - File System Caching](https://www.baeldung.com/linux/file-system-caching)

### Useful Links:

- [Performance Tuning Disks](https://cromwell-intl.com/open-source/performance-tuning/disks.html)
- [ArchLinux - Improving Performance](https://wiki.archlinux.org/title/improving_performance)
- [Baeldung - File System Caching](https://www.baeldung.com/linux/file-system-caching)

---

## Erigon Flags

```
./build/bin/erigon --datadir="/root/bsc-erigon" --chain=bsc --db.pagesize=16k --port=30303 --http.port=8545 --rpc.batch.concurrency=128 --db.read.concurrency=192 --p2p.protocol=67 --authrpc.port=8551 --torrent.port=42069 --snapshots --private.api.addr=127.0.0.1:9090 --http --ws --http.api=eth,erigon,web3,net,debug,trace,txpool,admin --maxpeers 300 --batchSize=8096M --bodies.cache=8037418240 --etl.bufferSize=16096M --state.cache=8096M --prune=hrtc --sentry.drop-useless-peers
```

I haven't seen much of a difference by increasing the cache size. The most significant difference will likely come from fast NVMe drives with low latency. If you have plenty of RAM available, set the page cache settings aggressively since Erigon doesn't manage the memory cache and expects the OS to handle it.

---

I hope this helps! Many people face challenges syncing Erigon, even on decent hardware. I'm not a Linux expert, but I've gathered this information online. If you have any useful tips to enhance Erigon's performance, please contribute!

---
#
