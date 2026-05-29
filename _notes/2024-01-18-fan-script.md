---
title:       "GL.iNet Fan Monitoring Script"
description: ""
tags:        [GL.iNet]
redirect_from:
 - /2024/01/18/fan-script.html
---

A short script to keep an eye on the fan status of the Slate AX.

Percentage is calculated a bit jankily because shell scripts lack floating point.  

The argument to sleep is a bit of a magic number, because changing that will also require updating the running time calculation math.

The script should be put on the router and assumes you are connected to the router with `ssh`.

```
#! /bin/sh

fan_on=0
fan_polls=0

echo "Starting fan monitoring..."

while true
do
    let fan_polls+=1
    fan_speed=$(cat /sys/class/thermal/cooling_device0/cur_state)

    echo -e -n "\rTime: " 
    echo -n "$(($fan_polls/12)) min   "

    echo -n "Temp: " 
    echo -n "$(cat /sys/devices/virtual/thermal/thermal_zone0/temp)"
    echo -n " C   "             
                                 
    echo -n "Fan: "       
    if [ $fan_speed -gt 0 ]; then
        let fan_on+=1   
        echo -n "ON    "
    else                       
        echo -n "OFF   "                        
    fi                                          
                                                
    echo -n "Duty Cycle: "                  
    echo -n "$(( ($fan_on * 100) / $fan_polls))"
    echo -n "%   "
           
    sleep 5
done
```
