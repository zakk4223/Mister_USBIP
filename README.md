# Forward mister input to another (linux) PC via the network

Mister's kernel has usbip compiled in by default, so we can use that to forward our mister attached controls to another PC. Maybe this PC could also run groovymister....

### On your MiSTer:

1. Download the two binaries in this repo (in the 'bin' directory). Put them somewhere on your mister. Maybe in /media/fat/linux/usbip

2. Run `/media/fat/linux/usbip/usbipd -D` 

3. Run `/media/fat/linux/usbip/usbip list -l`

```/root# /media/fat/linux/usbip/usbip list -l
 - busid 1-1.5 (1532:0258)
   Razer USA, Ltd : unknown product (1532:0258)

 - busid 1-1.6.2 (054c:09cc)
   Sony Corp. : DualShock 4 [CUH-ZCT2x] (054c:09cc)

 - busid 1-1.6.3 (045e:02fe)
   Microsoft Corp. : unknown product (045e:02fe)
```

4. Find the controller/input you want to forward to the remote host, remember the 'busid'. In this example we'll forward the Dualshock 4, busid 1-1.6.2

5. Run `/media/fat/linux/usbip/usbip bind --busid 1-1.6.2` (replace with the busid of your device)

### On the remote host (linux)

1. Depending on your distro/kernel/etc you may need to load the kernel modules vhci-hcd and usbip-core
   (modprobe usbip-core; modprobe vhci-hcd)

2. Run `usbip list --remote <mister hostname or ip address>`

```[zakk@blkheart:~][1]$ usbip list --remote 192.168.1.161
Exportable USB devices
======================
 - 192.168.1.161
    1-1.6.2: Sony Corp. : DualShock 4 [CUH-ZCT2x] (054c:09cc)
           : /sys/devices/platform/soc/ffb40000.usb/usb1/1-1/1-1.6/1-1.6.2
           : (Defined at Interface level) (00/00/00)
           :  0 - Audio / Control Device / unknown protocol (01/01/00)
           :  1 - Audio / Streaming / unknown protocol (01/02/00)
           :  2 - Audio / Streaming / unknown protocol (01/02/00)
           :  3 - Human Interface Device / No Subclass / None (03/00/00)
```

3. Now attach the usb device to our local machine. You need root privs to do this:
`sudo usbip attach  --remote 192.168.1.161 --busid 1-1.6.2`

The Dualshock 4 should now appear as a normal USB device on your PC. 
```
[zakk@blkheart:~]$ lsusb -d 054c:09cc
Bus 009 Device 005: ID 054c:09cc Sony Corp. DualShock 4 [CUH-ZCT2x]
```

### I want to undo this and use my controller normally on the mister!

#### Fast way: unplug it and plug it back in.

#### Slow(er) way: stop exporting it
1. On the MiSTer run `/media/fat/linux/usbip/usbip unbind --busid 1-1.6.2`


### Latency?

I have no good way to test the overall impact to latency when using something like groovymister. It seemed good enough that I was able to play comfortably, but 'I didn't notice it' latency tests are bullshit. Don't trust me.
Someone with the infrastructure setup to do this should test it.

### Windows?
I have no idea. I think there are a number of usbip windows implementations out there. If someone can get one working acceptably let me know/send a PR and I'll add it here.


