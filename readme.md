# Linux Optimations

Even if you have a fast nvme setup doing some page cache optimations maybe give you a little boost.

1. File System
Make sure you are using ext4 or xfs as they are most commnonly used and their performance on nvmes is well known.

2. IO Scheduler

Check your nvme scheduler if its already set to none using this command:
cat /sys/block/nvme<id>/queue/scheduler

If you dont know your nvme id you can use a tool like iostat to see the ids

3. Install IRQ Balance
If you havent already you can install it:
sudo apt install irqbalance
systemctl start irqbalance
systemctl status irqbalance

more info: https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/6/html/performance_tuning_guide/sect-red_hat_enterprise_linux-performance_tuning_guide-tool_reference-irqbalance

4. Disabling noatime and setting commit=60

If you have powerful nvmes probably it wont do anything but its good to have it

Edit the /etc/fstab and find your disk name and inlcude the noatime,commit=60, if you are raid 0 it will be probably be something like md0

Example config:

# /etc/fstab: static file system information.
#
# Use 'blkid' to print the universally unique identifier for a
# device; this may be used with UUID= as a more robust way to name devices
# that works even if disks are added and removed. See fstab(5).
#
# <file system> <mount point>   <type>  <options>       <dump>  <pass>
# / was on /dev/md0 during installation
UUID=c74d59d4-cfea-4372-99ba-e7c1a7f0baae /               ext4    errors=remount-ro,noatime,commit=60 0       1
# /boot/efi was on /dev/nvme0n1p1 during installation
UUID=5851-B0E6  /boot/efi       vfat    umask=0077      0       1
/swapfile                                 none            swap    sw              0       0

For the changes to take effect you should remount or reboot

Check if the changes are applied:
cat /proc/mounts | grep ' / '

5. Enabling Fast Commit

If you are on kernel 5.10 or later you can enable the fast commit future
Ubuntu 22.04 already are on 5.15+ but you can always check your kernel version:

uname -r

If your version supports it you can enable it by running:
sudo tune2fs -O fast_commit /dev/<mountid>

And see if the changes applied:
sudo tune2fs -l /dev/<mountid> | grep features


------------------------
Page Cache Optimizations
------------------------
# Setting vfs cache values

You can save this settings in your etc/sysctl.conf so they can be permanent

# Speed Optimizations
vm.swappiness=0 # By setting the swappiness to zero we dont use the swap memory at all or we can completly disable it by running sudo swapoff -a
vm.vfs_cache_pressure=30
vm.dirty_background_ratio=20
vm.dirty_ratio=35
vm.dirty_expire_centisecs=6000
vm.dirty_writeback_centisecs=500

For these changes to be applied run sudo sysctl -p
Its always a good idea to also reboot
This is just an example you should adjust these values according to your hardware

source: https://www.baeldung.com/linux/file-system-caching

useful links:
https://cromwell-intl.com/open-source/performance-tuning/disks.html
https://wiki.archlinux.org/title/improving_performance
https://www.baeldung.com/linux/file-system-caching

Erigon Flags

./build/bin/erigon --datadir="/root/bsc-erigon" --chain=bsc --db.pagesize=16k --port=30303 --http.port=8545 --rpc.batch.concurrency=128 --db.read.concurrency=192 --p2p.protocol=67 --authrpc.port=8551 --torrent.port=42069 --snapshots --private.api.addr=127.0.0.1:9090 --http --ws --http.api=eth,erigon,web3,net,debug,trace,txpool,admin --maxpeers 300 --batchSize=8096M --bodies.cache=8037418240 --etl.bufferSize=16096M --state.cache=8096M --prune=hrtc --sentry.drop-useless-peers

I havent seen much a difference by bumping the cache size, i think the big difference will be made mostly by fast nvmes with low latency and if you have plent of ram available you set the page cache settings aggressivly since Erigon doesnt manage the memory cache and expects from the OS to give it.

I hope this helps out since a lot of people have trouble syncing Erigon even on decent hardware, Im not a linux expert i just gathered any information i found online if you have any useful tip that can help with Erigon's performance please feel free to contribute!