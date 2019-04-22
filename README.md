# 8bitcomputer
8bit computer build project readme  

# Introduction
Some time ago I've stumbled upon a video series on YT done by Ben Eater (https://www.youtube.com/watch?v=HyznrdDSSGM&list=PLowKtXNTBypGqImE405J2565dvjafglHU) explaining in great detail how to build an 8bit computer. Finally, all parts (apart from two - I don't need them just yet though) from 5 different vendors are here so the build can start!

Overall, electronics in general has been a topic I wanted to dig into some more for quite a while now so I thought building an 8bit from stratch can't be *that* hard, right? Also, I've always wanted to thoroughly understand how computer *really* works "under the hood". And since the principles are still the same, I should be able to learn something from it, too.

Let's find out. I don't have any electronics background, just did some programming/testing in my spare time/at work so I'm curious myself how long it's gonna take.

# Clock Module
So far so good, looks like I've still got all the parts I need and first Clock Module prototype seems to be working:
https://www.youtube.com/watch?v=LTpQNR-XC3s

04.03.2019 - Debouncing circuit for clock module (https://www.youtube.com/watch?v=6O6xdOexSCE)
The whole purpose of the debouncing circuit is to prevent current jumps on the tact switch - it might seem the switch will always trigger just one clock pulse but it actually might do it more than once with one click - if the metal plates inside of the switch will bump against each other more than once under motion. This might get very problematic when using the clock for debugging of to-be-built components.

The circuit is using 555 timer working monostable - which basically means is has one stable state until we press the switch. Then, depending on the size of transistor and capacitor, the time when LED lights up may vary. In my example it's more or less 1 second:
1M resistor (1 000 000 ohms) x 1uF capacitor (1E-06)

1000000 x 1E-06 = 1sec

Also, despite the fact that the voltage I'm using is higher (9v battery instead of 5v Ben is using), the time the LED lights up is the same - this is because the 3 resistors inside of NE555 are acting as reference for SR latch to switch - and voltage on each of those it gonna be higher due to 9v battery (6v and 3v respectively). So, the 1M capacitor is charged faster on 9V battery, but then the resistors also need  higher reference voltage to trigger the SR latch - thus the effect cancels out = same time for LED to light up.

Now I need to figure out the voltage divider for LS chips cause they won't work correctly on 9v battery. Or just change the battery to lower voltage...

15.04.2019 - adding 7805 linear voltage regulator allowed for moderating voltage 5v - as required by LS chips:
https://photos.app.goo.gl/yx3v7NWd7WakuEx16
https://photos.app.goo.gl/n6rvW5TRZJwUxog56

22.04.2019 - added 3 LS chips:
  - 1x 74LS04 (Hex inverter) 
  - 1x 74LS08 (Quad AND gate) 
  - 1x 74LS32 (Quad OR gate)
that together control the clock output (be it monostable or astable ticks over 555 timers):
https://imgur.com/a/uG5DH8F
![My image](https://i.imgur.com/Z9cc8RK.png)
(taken from https://youtu.be/SmQ5K7UQPMM)

So depending on the position of select switch, it will allow for switching between the two modes, the blue LED eventually outputting the current mode, mimmicking one of the astable / monostable yellow LEDs we already did before.  

![My image](https://i.imgur.com/saG1DRD.jpg)

To put it in different perspective:
![My image](https://i.imgur.com/9yB29dA.png)

Astable, monostable and select voltage sources would be of course coming from OUT pins of each respective 555 timers.

On the example of an astable pulse, first AND gate will allow the current flow only when select switch is allowing current to flow to it and astable pulse is 1 (yellow LED is lit up). Then, OR gate allows the current to pass further cause for OR gate it's sufficient for the current to flow only from one source to pass current on. Eventually, current reaches the last AND gate that's receiving current from the just described source, but also from inverted HALT jumper wire. 

# Registers
The fun part starts, I guess ;)
