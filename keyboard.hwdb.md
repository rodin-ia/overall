Ubuntu
` apt install evemu-tools evtest `

get event
` sudo evtest `

get udevinfo
` sudo udevadm info /dev/input/event* `

get DMI modalias
` cat /sys/class/dmi/id/modalias `

add custom hwdb in /etc/udev/hwdb.d/90-custom-keyboard.hwdb
```
evdev:atkbd:dmi:bvn*:bvr*:bd*:svnHP*:pnHPEliteBook845G7NotebookPC*:pvr*
 KEYBOARD_KEY_f8=!wlan   # fn+F11: airplane mode activation 
```
