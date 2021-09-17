# HX_camera_hacking
Fooling around with HiChip-compatible Chinese CCTV surveillance cameras

## Introduction
I will call this camera type the "HX" camera, which is what SV3C describes [their camera series as](https://sv3c.com/ip-camera/hx-series-ip-camera/wireless-ip-camera/), and the XML onvif on the camera also refers to this name. But this camera/firmware spans hundreds of manufacturers who all use this same OEM firmware and run it on a variety of SoCs (Hi3516/Hi3518, ingenic T21/T31). Another way to find and identify these cameras is that they are compatible with the iOS/Android apps "CamHi" and "CamHiPro". They also have an HTTP server that identifies as "Hipcam".

The web interface looks like this

![Screenshot 2021-09-17 08 19 31](https://user-images.githubusercontent.com/278457/133809215-033db9ee-e740-4cbf-b3b5-eac51de2653d.png)

And has URLs like `/cgi-bin/hi3510/param.cgi`.

Anyway, my goal here is to be able to customize the camera rather than just find exploits. Things like sync jobs, NFS mounts, other automation. If the manufacturer reads this, it would be really cool if you made our lives easier by just having an "Enable telnet" checkbox in the UI, with a warning or something.

## Good hardware, for a great price

There is a lot of negative chatter about these cameras on ipcamtalk forums, etc. Understandably, they are cheap, China-sourced cams that are not meant to compete with Arecont Vision, IQinvision, or even Dahua or Hikvision cams. But those cams cost a bunch more, and require wired networking.

A lot can be said about a device with an 864MHz CPU, memory, wifi, ethernet, CMOS camera, hardware H.264/265 encoding, audio in/out, SD card interface, PTZ control servos, a decently-defined [CGI API](http://www.notesco.net/download/H218W_CD_cgi/FC%20IP%20Camera%20CGI%20User%20Manual.doc), and already-developed, useful features (NTP sync, FTP xfer, HTTP serving of SD card content) for the low price of US$35. To get equivalent capabilities on a Raspberry Pi zero for example would be about $50+, plus dev time, and it would not be nearly as integrated, nor in a nice, waterproof package.

## Hardware example

```
~ # cat /proc/cpuinfo 
system type             : Turkey
machine                 : Unknown
processor               : 0
cpu model               : Ingenic Xburst V0.0  FPU V0.0
BogoMIPS                : 858.52
wait instruction        : yes
microsecond timers      : no
tlb_entries             : 32
extra interrupt vector  : yes
hardware watchpoint     : yes, count: 1, address/irw mask: [0x0fff]
isa                     : mips32r1
ASEs implemented        :
shadow register sets    : 1
kscratch registers      : 7
core                    : 0
VCED exceptions         : not available
VCEI exceptions         : not available

Hardware                : isvp
Serial                  : 00000000 00000000 00000000 00000000
```

```
~ # lsusb
Bus 001 Device 002: ID 148f:7601
Bus 001 Device 001: ID 1d6b:0002
```

`148f:7601` is the RAlink rt2870 USB WiFi adapter.

Selected dmesg lines:
```
[    0.000000] Linux version 3.10.14__isvp_turkey_1.0__ (root@localhost.localdomain) (gcc version 4.7.2 (Ingenic r2.3.3 2016.12) ) #28 PREEMPT Wed Mar 17 22:47:35 HKT 2021
[    0.000000] CCLK:864MHz L2CLK:432Mhz H0CLK:200MHz H2CLK:200Mhz PCLK:100Mhz
[    0.000000] Memory: 39320k/44672k available (3419k kernel code, 5352k reserved, 655k data, 212k init, 0k highmem)
[   18.536574] sc2335 chip found @ 0x30 (i2c0)
```

### Camera models

* `C6F0SgZ0N0PhL2` - PTZ 1080p (no zoom)
* `C6F0SoZ3N0PdL2` - stationary 5mp

Still working on decoding the model attributes...the model ID is some kind of generic identifier

## Invaluable resources
* https://github.com/guino/WR301CH1KW for ideas around using `restore_config.bin` to take control of the camera
* https://research.nccgroup.com/2020/07/31/lights- camera-hacked-an-insight-into-the-world-of-popular-ip-cameras/
* https://www.mauritsvanaltvorst.com/rce-insecure-ip-camera for injection ideas
* http://counteratk.blogspot.com/2018/05/
* https://sysdream.com/news/lab/2020-01-03-iot-pentest-of-a-connected-camera/
* http://www.wolfteck.com/2019/03/07/getting_into_the_ctipc-275c1080p/
