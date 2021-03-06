## apu-led-button

Linux kernel module for PC Engine's APU system board to access the three front LEDs and the reset button. Originally published [here](http://daduke.org/linux/apu/) but slightly modified for Ubuntu Server 14.04 and newer. 

After the installation, the first LED will indicate if the system is up using ledtrig-timer. You could also use ledtrig-heartbeat instead if you prefer a more active status LED. The second and third LED will act as RX/TX indicators for the network port of your choice. The configuration sample below uses eth1 because it's the WAN port in my setup. Additionally, pressing the front reset button for at least a second will issue a beep and soft-reboot the APU.

See [here for more trips and tricks for the APU](https://trick77.com/tips-tricks-for-the-pc-engines-apu/).

## Installation

```
# sudo apt-get install linux-headers-$(uname -r) build-essential
# git clone https://github.com/trick77/apu-led-button
# cd apu-led-button
# make && sudo make install
```

(as root)
```
# echo "ledtrig-timer" >> /etc/modules
# echo "ledtrig-heartbeat" >> /etc/modules
# echo "apuled-button" >> /etc/modules
```

## Sample configuration

Insert these lines to /etc/rc.local just above exit 0:

```
echo "timer" > /sys/class/leds/apu\:1/trigger
echo "1750" > /sys/class/leds/apu:\1/delay_on
echo "250" > /sys/class/leds/apu\:1/delay_off
/usr/local/sbin/apuled eth1 -c nrt -f
/usr/local/sbin/apubutton&
```

...and reboot.

The LEDs trigger capabilities are listed in <code>/sys/class/leds/apu\:1/trigger</code> with the currently active trigger marked in brackets.

<pre>
# cat /sys/class/leds/apu\:1/trigger
none usb-gadget usb-host cpu0 cpu1 [timer] rfkill0 phy0rx phy0tx phy0assoc phy0radio phy0tpt heartbeat
</pre>

## License
* Copyright &copy; 2014, Mark Schank
* Copyright &copy; 2013, Christian Herzog

This program is free software; you can redistribute it and/or modify it under the terms of the GNU General Public License version 2 as published by the Free Software Foundation.
