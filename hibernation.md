hibernation in ubuntu 22.04 via swap file
```
sudo fallocate -l 16G /home/swapfile 
sudo chmod 600 /home/swapfile
sudo mkswap /home/swapfile 
sudo swapon /home/swapfile
```
edit /etc/fstab, add swapfile
```
/swapfile                            none            swap    sw              0       2
```
get offset and UUID
and edit /etc/default/grub
```
OFFSET=`sudo filefrag -v /home/swapfile |grep " 0:"| sed s'/\.//g' | awk '{print $4}'`
MP=`df /home/swapfile | grep -v 'Filesystem' | awk '{print $1}'`
echo "resume="`blkid ${MP} | awk '{print $3}' | sed 's/"//g'`" resume_offset=${OFFSET}"
```
als swap file in home partition, mod systemd service
```
systemct edit systemd-logind.service
```
and add
```
[Service] 

ProtectHome=read-only
```
Update the GRUB using ```update-grub```
