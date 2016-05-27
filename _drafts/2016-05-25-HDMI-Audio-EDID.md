---
title: "HDMI Audio and EDID"
author: "Awen"
date:  2016-05-25 19:10:44 -0700
layout: post
categories:
---

I recently decided to switch the in-wall cabling for my projector from  to HDMI. This resulted in a huge improvement in video quality, but introduced a number of new and interesting bugs. I bought a [KanexPro 4x1 HDMI switch with optical audio](http://www.kanexpro.com/item/?id=SW-HD4X1AUD4K) to switch inputs and output audio to my Pioneer reciever (which is too old to support HDMI). My PS3 was the only device that worked with this setup initially, as all my other devices were unwilling to send audio over the HDMI connection to a projector that did not report audio support. 

### EDID -- Extended Display Identification Data

EDID is the data sent from a digital display to a video source in order to communicate its capabilities and video timing. HDMI uses an extended version of this structure called E-EDID which has an additional 128 byte segment to communicate advanced capabilities such as audio codec compatibility and HD video support. An excellent description of the spec is available at [Wikipedia](https://en.wikipedia.org/wiki/Extended_Display_Identification_Data). In HDMI, E-EDID is communicated from the display to the source over a protocol called DDC (which is actually just I<sup>2</sup>C on pins 15 and 16 of the HDMI cable). 

### The Problem

Using the `ioreg -l | grep 'IODisplayEDID'` command on my Mac, I was able to view the raw data in my projector's edid. It is also possible to do this in Linux with the `read-edid` package. 

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


After reading the Wikpedia article on the EDID spec, I discovered that audio capabilites are communicated in two ways. Firstly, in byte 03 of the E-EDID extension block (offset `0x83`), and secondly in three byte segments called SADs. 





*[E-EDID]: Enhaanced Extended Display Identification Data
*[DDC]: Display Data Channel
*[SAD]: Short Audio Descriptor
*[SADs]: Short Audio Descriptors