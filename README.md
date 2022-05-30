## libguestfs-docker
libguestfs-tools in docker container


### Build
```
docker build -t libguestfs-docker .
```

### HOWTO shrink vdisk space with this conainer (for example to 20G):

### Run docker
```
docker run --rm -it -v /user/disks:/disks libguestfs-docker
```

### Check disk
```
virt-df -h /disks/vdisk1.img
virt-filesystems --long --parts --blkdevs -h -a /disks/vdisk1.img
```

### Guestfish
```
guestfish -a /disks/vdisk1.img
run
list-filesystems
lvs
# -> /dev/ubuntu-vg/ubuntu-lv
pvs
# -> /dev/sda3
e2fsck-f /dev/ubuntu-vg/ubuntu-lv
resize2fs-size /dev/ubuntu-vg/ubuntu-lv 16G
lvresize /dev/ubuntu-vg/ubuntu-lv 16384
# main partition size -> 16G
pvresize-size /dev/sda3 17G
# have to be bigger than logical disk space 17G > 16G
exit
```

### Check resized disk space
```
virt-df -h /disks/vdisk1.img
virt-filesystems --long --parts --blkdevs -h -a /disks/vdisk1.img
```

### Create disk with target size
```
truncate -s 20G /disks/vdisk1_new.img
virt-resize --shrink /dev/sda3 /disks/vdisk1.img /disks/vdisk1_new.img
```

### Check new disk space
```
virt-df -h /disks/vdisk1_new.img
virt-filesystems --long --parts --blkdevs -h -a /disks/vdisk1_new.img
```