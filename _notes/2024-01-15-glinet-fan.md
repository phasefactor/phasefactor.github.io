---
title:       "Setting GL.iNet Slate AX Fan Temperature"
description: "Two ways to set a custom fan temperature."
tags:        GL.iNet
redirect_from:
 - /2024/01/15/glinet-fan.html
---

The default fan temperature threshold for GL.iNet devices can be set a bit high.  

Lower temperatures will make the device last longer, but we should avoid running the fan so much that it fails prematurely.

For maximum longevity in a home router setup, consider using a small external USB powered fan to further cool the router.

- TOC
{:toc}


---

## Setting Fan Temperature via Web Interface
GL.iNet's web interface supports setting the fan temperature.  
- Log into the router.
- From the navigation menu select **SYSTEM** and click **Overview**.
- Click the fan and gear wheel icon in the upper right corner of the **CPU Average Load** section.
- Set your desired temperature and click **Apply** to save.

The web interface is limited to setting temperatures between 70 and 90 Celcius, but temperatures lower than 70 C would be better for maximum longevity of the device.


---

## Setting Fan Temperature via SSH

### Connecting to the Router
At the command line on your computer:
- Run `ssh root@192.168.8.1` (or whatever IP address you have set for your router).
- Login with the password you configured during initial setup of the router’s web interface.


### Option 1: Editing `/etc/config/glfan` Directly
To do this:
  - Run `vi /etc/config/glfan`
  - Press `i` to enter edit mode.
  - Edit the line `option temperature '60'` to the desired temperature.
  - Press `ESC` to exit edit mode.
  - Type `:wq` and hit Enter to save and quit.

Reboot the router or restart the gl_fan service with `/etc/init.d/gl_fan restart`.


### Option 2: Using Universal Configuration Interface (UCI)
```
uci set glfan.@globals[0].temperature='60'
uci commit glfan
```

Reboot the router or restart the gl_fan service with `/etc/init.d/gl_fan restart`.

---

## Choosing a Temperature
Using the settings mentioned in this [GL.iNet forum post](https://forum.gl-inet.com/t/i-found-a-way-to-set-the-gl-fan/22274/6) keeps the router at 60 C with a relatively low duty cycle.

There are instructions in the post, but I put the settings in my `/etc/config/glfan` file directly:
```
config globals 'globals'
	option enabled '1'
	option sysfs '/sys/devices/virtual/thermal/thermal_zone0/temp'
	option div '1'
	option warn_temperature '75'
	option temperature '60'
	option integration '4'
	option differential '20'
```

Whatever settings used, try the [fan monitoring script]({% link _notes/2024-01-18-fan-script.md %}) to see the resulting duty cycle.


---

## Useful Commands
To use these, you must SSH into the router: `ssh root@192.168.8.1`

### Starting/Stopping/Restarting the gl_fan service
- `/etc/init.d/gl_fan start`
- `/etc/init.d/gl_fan stop`
- `/etc/init.d/gl_fan restart`


The gl_fan service starts on bootup by default, but you can stop it and set fan speeds manually.

### Set Fan Speed Manually
`echo 255 > /sys/class/thermal/cooling_device0/cur_state`

This directly sets the fan speed.  A value of 255 corresponds to the maximum setting of 6000rpm.

### Checking Current Temperature
`cat /sys/devices/virtual/thermal/thermal_zone0/temp`

