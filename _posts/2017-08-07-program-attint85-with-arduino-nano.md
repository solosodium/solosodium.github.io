---
layout: post
title: Program ATtiny85 with Arduino Nano
published: true
---

Recently I was working on a project with Arduino Nano. To fit the entire project into a 3D printed closure with limited space, I was searching for ways to shrink down the physical footprint of the Arduino Nano board. That's when I came across this [video](https://www.youtube.com/watch?v=30rPt802n1k), in which it mentioned the [ATtiny85](http://www.microchip.com/wwwproducts/en/ATtiny85) chip. It is a variant of a family of Arduino compatible 8-bit controllers with only 8 pins. The small form factor fits the bill perfectly, plus I can use the same code for Nano as long as it can fit into chip's 8KB memory.

![ATtiny85 chip, source: https://solarbotics.com/product/28904/](/custom/2017-08-07-program-attint85-with-arduino-nano/attiny85.jpg "ATtiny85 chip, source: https://solarbotics.com/product/28904/")

After some digging, it turns out the video was based on two blog posts from MIT Media Lab's [High-Low Tech group](http://highlowtech.org/): [Programming an ATtiny w/ Arduino 1.6 (or 1.0)](http://highlowtech.org/?p=1695) and [Arduino board as ATtiny programmer](http://highlowtech.org/?p=1706). The idea behind the posts is really simple: use Arduino Uno as an in-system programmer ([ISP](https://en.wikipedia.org/wiki/In-system_programming)) to upload Arduino code to ATtiny85. Albeit I only have an Arduino Nano, the instructions are quite straightforward to follow. However, judging from the software versions, the posts were there for a while, and I did run into some troubles. So I decide to summarize the procedures I took to make it work.

## # Load Arduino Nano with ISP Program

Step 1: open Arduino IDE (for this, I am using the newest version of 1.8.3), and open the **ArduinoISP** sketch in **Examples**.

![ISP 1](/custom/2017-08-07-program-attint85-with-arduino-nano/isp1.png "ISP 1")

Step 2: make sure the programmer is set to **AVRISP mkII**, because we need to upload the sketch to Nano first.

![ISP 2](/custom/2017-08-07-program-attint85-with-arduino-nano/isp2.png "ISP 2")

Step 3: upload it and... Done! (Well, for now...)

## # Wire Up

Follow this wiring diagram to connect ATtiny85 with Arduino Nano.

![Wiring diagram](/custom/2017-08-07-program-attint85-with-arduino-nano/wu1.png "Wiring diagram")

Here are some tips:

* You need a 10uF capacitor between Nano's reset (RST) and ground (GND) to prevent Nano from resetting during upload
* Nano pin 13 -> ATtiny85 pin 2
* Nano pin 12 -> ATtiny85 pin 1
* Nano pin 11 -> ATtiny85 pin 0
* Nano pin 10 -> ATtiny85 pin Reset

Nano's pins are clearly labeled on the board. In case you are wondering the pin numbers on ATtiny85, here is the pin layout diagram.

![ATtiny85 pin layout, source: http://highlowtech.org/?p=1695](/custom/2017-08-07-program-attint85-with-arduino-nano/wu2.png "ATtiny85 pin layout, source: http://highlowtech.org/?p=1695")

## # Install ATtiny Boards

Step 1: open **Preferences** in Arduino IDE, and add *https://raw.githubusercontent.com/damellis/attiny/ide-1.6.x-boards-manager/package_damellis_attiny_index.json* to **Additional Boards Manager URLs**.

![Install ATtiny boards 1](/custom/2017-08-07-program-attint85-with-arduino-nano/ia1.png "Install ATtiny boards 1")

Step 2: open **Board Manager** and install **attiny** boards.

![Install ATtiny boards 2](/custom/2017-08-07-program-attint85-with-arduino-nano/ia2.png "Install ATtiny boards 2")

![Install ATtiny boards 3](/custom/2017-08-07-program-attint85-with-arduino-nano/ia3.png "Install ATtiny boards 3")

## # Upload Code

Now we can finally upload your code to ATtiny85. But before that, you need to select the right board settings as the following, and make sure to select programmer as **Arduino as ISP**.

![Upload code](/custom/2017-08-07-program-attint85-with-arduino-nano/uc.png "Upload code")

Upload the code, DONE and DONE!
