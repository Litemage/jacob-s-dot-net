---
title: "I2C I-O Expander With Driver"
description: "This project was about developing and manufacturing my first PCB, an I2C I/O expander board carrying a PI4IOE5V6416 16-channel I/O expander from Diodes Inc."
author: "Jacob Simeone"
pubDate: "2024-05-19"
updatedDate: "2024-05-19"
coverImage: "/posts/io-expander/Board_Front.png"
tags: ""
---

# Introduction to the I/O Expander

This [project](https://github.com/Litemage/PI4IOE5V6416_Breakout) was about developing and manufacturing my first PCB, an I2C I/O expander board carrying a PI4IOE5V6416 16-channel I/O expander from Diodes Inc. Linked [here](https://www.mouser.com/ProductDetail/Diodes-Incorporated/PI4IOE5V6416ZDEX?qs=byeeYqUIh0OndNU%2F4OrZvQ%3D%3D).

The board features two solder pads, JP1 and JP2 which are for v-bus and I2C address selection respectively. 

The only other IC on the board is the LDO linear regulator I chose, the [LP5907](https://www.ti.com/lit/ds/symlink/lp5907.pdf?ts=1719065674591&ref_url=https%253A%252F%252Fwww.mouser.it%252F), which gives the I/O expander IC it's 3V3 rail from 0-6V, which is pretty much what we'd find on any Arduino-compatible hardware. I should note this particular version of the board uses 3.3V logic. 

# Software Support

To go along with the board, I developed a generic C driver for the board. I developed the library in a way that makes it easy for users to implement the board into their specific development environment. Unfortunately, I have not gotten around to developing an Arduino library for this particular board, but I may revisit it at some point. 

Users of the library only need to define 4 functions that will control the physical interfaces of the I/O expander, and add those functions to the device handle. The four functions needing importing is the **write register function**, **read register function**, **reset pin function**, and the **delay microseconds function**. 

Referencing the [headers](https://github.com/Litemage/PI4IOE5V6416_Breakout/blob/master/Driver/C/PI4IOE5V6416/PI4IOE5V6416_defs.h) for the function pointers goes into more detail about how to implement these functions. More detail can be found in the datasheet for the I/O expander. 