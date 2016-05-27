---
title: "HDMI Audio and EDID"
author: "Awen"
date:  2016-05-25 19:10:44 -0700
layout: post
categories:
---

I recently decided to switch the in-wall cabling for my projector from  to HDMI. This resulted in a huge improvement in video quality, but introduced a number of new and interesting bugs. I bought a [KanexPro 4x1 HDMI switch with optical audio](http://www.kanexpro.com/item/?id=SW-HD4X1AUD4K) to switch inputs and output audio to my Pioneer receiver (which is too old to support HDMI). My PS3 was the only device that worked with this setup initially, as all my other devices were unwilling to send audio over the HDMI connection to a projector that did not report audio support. 

### EDID -- Extended Display Identification Data

EDID is the data sent from a digital display to a video source in order to communicate its capabilities and video timing. HDMI uses an extended version of this structure called E-EDID which has an additional 128 byte segment to communicate advanced capabilities such as audio codec compatibility and HD video support. An excellent description of the spec is available at [Wikipedia](https://en.wikipedia.org/wiki/Extended_Display_Identification_Data). In HDMI, E-EDID is communicated from the display to the source over a protocol called DDC (which is actually just I<sup>2</sup>C on pins 15 and 16 of the HDMI cable). 

### The Problem

Using the `ioreg -l | grep 'IODisplayEDID'` command on my Mac, I was able to view the raw data in my projector's EDID. It is also possible to do this in Linux with the `read-edid` package. Upon decoding the EDID with [edid-decode](https://cgit.freedesktop.org/xorg/app/edid-decode/), I determined that the problem was in fact with the EDID of my projector, which listed no audio capabilities. 

The original EDID:

    0000000: 00 ff ff ff ff ff ff 00 3e 14 20 00 fa 00 00 00
    0000010: 0e 14 01 03 80 00 00 78 0a 1c 43 a4 57 4b a8 26
    0000020: 0c 49 4e ff ff 80 71 40 81 40 61 59 81 00 81 80
    0000030: 90 40 81 c0 a9 40 02 3a 80 18 71 38 2d 40 58 2c
    0000040: 35 00 c4 8e 21 00 00 1c 0e 1f 00 80 51 00 1e 30
    0000050: 40 80 37 00 c4 8e 21 00 00 1c 00 00 00 fc 00 4f
    0000060: 70 74 6f 6d 61 20 48 44 32 30 0a 20 00 00 00 fd
    0000070: 00 0f 58 0f 64 11 00 0a 20 20 20 20 20 20 01 db
    0000080: 02 03 17 31 4b 90 1f 05 14 04 22 21 13 03 12 20
    0000090: 66 03 0c 00 10 00 28 f3 39 80 18 71 38 2d 40 58
    00000a0: 2c 45 00 c4 8e 21 00 00 1e f3 39 80 d0 72 38 2d
    00000b0: 40 10 2c 45 80 c4 8e 21 00 00 1e 01 1d 00 72 51
    00000c0: d0 1e 20 6e 28 55 00 c4 8e 21 00 00 1e 8c 0a d0
    00000d0: 90 20 40 31 20 0c 40 55 00 13 8e 21 00 00 18 8c
    00000e0: 0a d0 8a 20 e0 2d 10 10 3e 96 00 13 8e 21 00 00
    00000f0: 18 00 00 00 00 00 00 00 00 00 00 00 00 00 00 93


After reading the Wikipedia article on the EDID spec, I discovered that audio capabilities are communicated in two ways. Firstly, the third byte in the extension block (offset `0x83`) signals if a device supports basic audio. Secondly an audio data block of three byte SADs communicates detailed information about the audio supported by the device. Enabling basic audio support entailed changing bit 6 of byte `0x83` from a 0 to a 1 (`0x31` to `0x71`). Writing the audio data block proved somewhat more complicated. Full details of the spec are available on [Wikipedia](https://en.wikipedia.org/wiki/Extended_Display_Identification_Data#CEA_EDID_Timing_Extension_Version_3_data_format/). 
The audio data block I ended up with was `29 16 07 50 09 1F 07 3E 1F BC`, which lists support for Dolby Digital 6.1ch, Stereo LPCM 96/24, and DTS 6.1ch.

I was able to verify my new EDID settings with with `edid-decode`. This also computed the checksum of the E-EDID block, which was helpful. 

My custom EDID:

    0000000 00 ff ff ff ff ff ff 00 3e 14 20 00 fa 00 00 00
    0000010 0e 14 01 03 80 00 00 78 0a 1c 43 a4 57 4b a8 26
    0000020 0c 49 4e ff ff 80 71 40 81 40 61 59 81 00 81 80
    0000030 90 40 81 c0 a9 40 02 3a 80 18 71 38 2d 40 58 2c
    0000040 35 00 c4 8e 21 00 00 1c 0e 1f 00 80 51 00 1e 30
    0000050 40 80 37 00 c4 8e 21 00 00 1c 00 00 00 fc 00 4f
    0000060 70 74 6f 6d 61 20 48 44 32 30 0a 20 00 00 00 fd
    0000070 00 0f 58 0f 64 11 00 0a 20 20 20 20 20 20 01 db
    0000080 02 03 21 71 4b 90 1f 05 14 04 22 21 13 03 12 20
    0000090 66 03 0c 00 10 00 28 29 16 07 50 09 1f 07 3e 1f
    00000a0 bc f3 39 80 18 71 38 2d 40 58 2c 45 00 c4 8e 21
    00000b0 00 00 1e f3 39 80 d0 72 38 2d 40 10 2c 45 80 c4
    00000c0 8e 21 00 00 1e 01 1d 00 72 51 d0 1e 20 6e 28 55
    00000d0 00 c4 8e 21 00 00 1e 8c 0a d0 90 20 40 31 20 0c
    00000e0 40 55 00 13 8e 21 00 00 18 8c 0a d0 8a 20 e0 2d
    00000f0 10 10 3e 96 00 13 8e 21 00 00 18 00 00 00 00 6b

> Disclaimer: DO NOT flash this EDID to your device. They are different for every model of display. Doing so could render your display completely useless.

### Using the New EDID

EDID is usually stored in an EEPROM that is directly connected to the I<sup>2</sup>C bus of the source device via pins 16 and 17 of the HDMI cable. I originally planned to attach a new EEPROM (specifically [this](http://www.microchip.com/wwwproducts/en/24LCS22A)) to the HDMI cable with my new EDID flashed to it. As it turned out, that ended up being unnecessary. Upon reading the [service manual](http://www.optoma.co.uk/optomatechnical/sharing/HD20/Service%20Manuals/HD20_HD200X_HD2200_HD20LV_HD21_HD23%20Service%20manual%20v7.0.pdf) for my projector, I surmised that the EEPROM in the projector was not write-protected. After enabling support for userland I<sup>2</sup>C with `modprobe i2c-dev` and installing the `i2c-tools` package, I identified that the DDC on my laptop was on I<sup>2</sup>C bus 3 (it will always be at address `0x50`). I used the `i2cdump 3 0x50` command to dump the EDID onto my Linux laptop to verify its integrity. After a bit of experimentation, I was able to put together a simplistic Python script to write my new EDID to my projector. 


    #!/usr/bin/env python3
    import subprocess
    import time
    i2cbus = 3 #This is important. Check which bus your DDC is on with i2cdetect. 
    offset = 0x00 #Where to start writing
    data = [0x00, 0xFF, 0xFF, 0xFF, 0xFF,...] #Your EDID Goes here

    for value in data:
        print("value", hex(value), "offset", hex(offset)) #for debuging
        print(subprocess.check_output(["i2cset", "-y", str(i2cbus), "0x50", str(hex(offset)), str(hex(value))]))
        time.sleep(0.1) #Makes sure we finish before moving on
        offset += 1
{:.language-python}
> This script is also available as a [gist](https://gist.github.com/awensaunders/f538d6a61961fc20f9dc11b0c29c8ec0).

After running this script (as root) and verifying the results with `i2cdump`, I was confident that the EDID had been changed correctly. I removed all of the excess optical audio cables from my system, and I can now switch between all of my A/V sources with just one button. 

*[E-EDID]: Enhanced Extended Display Identification Data
*[DDC]: Display Data Channel
*[SAD]: Short Audio Descriptor
*[SADs]: Short Audio Descriptors