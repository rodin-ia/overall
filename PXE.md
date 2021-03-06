NM + dnsmasq + PXE + grub

1. config dnsmasq (NM) /etc/NetworkManager/dnsmasq-shared.d/pxe.conf

```
enable-tftp
tftp-no-blocksize
log-dhcp
pxe-prompt="Press any key for boot menu",5

dhcp-match=set:BIOS,option:client-arch,00000
dhcp-match=set:EFI,option:client-arch,00007

dhcp-boot=tag:BIOS,/boot/grub/i386-pc/core.0
dhcp-boot=tag:EFI,/boot/grub/x86_64-efi/core.efi

tftp-root=/srv/tftp/
```
2. Dir Tree /srv/
```
├── http
├── nfs
│   └── kubuntu
│       └── 2204
│           ├── boot
│           │   .disk
│           ├── [BOOT]
│           ├── casper
│           ├── dists
│           ├── EFI
│           ├── install
│           ├── pool
│           └── preseed
└── tftp
    ├── boot
    │   └── grub
    │       ├── fonts
    │       ├── i386-pc
    │       ├── locale
    │       └── x86_64-efi
    └── dist
        ├── freedos
        └── ubnt
            └── k
                ├── 2004
                └── 2204
```
3. generate grub
```
grub-mknetdir --net-directory=/srv/tftp/
```
4. /srv/tftp/boot/grub/grub.cfg
```
function load_video {
  insmod efi_gop
  insmod efi_uga
  insmod video_bochs
  insmod video_cirrus
  insmod all_video
}

load_video
set gfxpayload=keep
set gfxmode=auto
loadfont unicode
font=unicode
insmod gfxterm
insmod gzio
insmod png

submenu "Ubuntu based" {

  menuentry "Kubuntu 22.04 http" {
    linux dist/ubnt/k/2204/vmlinuz ip=dhcp url=http://10.42.0.1:8008/kubuntu-22.04-desktop-amd64.iso nosplash ---
    initrd dist/ubnt/k/2204/initrd
  }
  menuentry "Kubuntu 22.04 nfs" {
    linux dist/ubnt/k/2204/vmlinuz boot=casper ro ip=dhcp netboot=nfs nfsroot=10.42.0.1:/srv/nfs/kubuntu/2204 file=/cdrom/preseed/kubuntu.seed nosplash  ---
    initrd dist/ubnt/k/2204/initrd
  }

}

submenu "Legacy" {

  menuentry "FreeDOS" {
    linux16 dist/memdisk iso
    initrd16 dist/freedos/FD13LIVE.iso
  }

  menuentry "FreeDOS BIOS" {
    linux16 dist/memdisk iso
    initrd16 dist/freedos/FD_BIOS.iso
  }

}
```

.add extra files to ISO FreeDOS
```
mkisofs -o ../FD_BIOS.iso -q -l -N -boot-info-table -iso-level 4 -no-emul-boot -b isolinux/isolinux.bin -publisher "FD_BIOS" -A "FD_BIOS" -V FD_BIOS -v .
```
