---
title: "Laser Serial - An Experiment"
description: "As a quick weekend project, I wanted to see how easy it was to transfer serial data over a couple laser diodes and photo resistors."
author: "Jacob Simeone"
pubDate: "2025-01-11"
updatedDate: "2025-01-11"
coverImage: ""
tags: "interesting"
---

# Inspiration

For the most part, [fiber optics](https://en.wikipedia.org/wiki/Optical_fiber) are the backbone of a vast majority of high-speed internet connections, taking a digital light signal and bouncing it in a glass tube over and over again until it reaches it's destination. When pondering this technology, it dawned upon me that using light to transmit digital data isn't only optimal for high-speed applications, but I theorized could be used to be a good demonstration of how serial data is transferred. 

# UART? Over Lasers??

The idea flowed as such: two laser diodes, and two photo-resistors, capable of detecting when the opposing laser diode was on, and this link of laser-diode and photo-resistor would replace the wires of the [UART](https://www.analog.com/en/resources/analog-dialogue/articles/uart-a-hardware-communication-protocol.html) Tx/Rx pair, and transmit data. I would plan on transmitting the data slow enough that an onlooker could actually see the data getting transmitted. By extension, one could also break the laser beam and introduce communication errors interactively. 

<img src="/posts/laser-serial/laser-diagram.png">

This would require both a mechanical assembly (preferably 3d-printed) and software that is capable of transferring a small amount of data, slowly over this link.

# Mechanical Assembly

The mechanical assembly I came up with consists of a track, and two "sliders" that each contain one laser diode and one photo-resistor, which are then wired to a microcontroller. The CAD design for this assembly can be found [here, on OnShape](https://cad.onshape.com/documents/0c7ee166e9a1c06a3074dac1/w/8e840894a83ecf490ebfd890/e/c5396c6452272620619c324f), and is pictured below:

<img style="max-height: 500px;" src="/posts/laser-serial/laser-serial-track.png">

The design above allowed me to freely move the two sliders as close together as possible, which turned out to be a good decision because it was hard enough to get the laser diode and photo-resistor to line up.

# Results

Unfortunately, I do not have any photos or videos of this test set-up working, but it did! I was successfully able to transmit serial data (slowly) down a laser diode, and into a photo resistor on the other end, where it was able to read a zero or one, assembling a fixed package of "Hello, World!". It was also possible to interrupt the laser beam and corrupt the packet, and the controller reading the data would drop the packet.