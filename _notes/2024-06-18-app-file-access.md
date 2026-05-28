---
title:       "Track Files Accessed by Applications on MacOS"
tags:        [MacOS, Xcode]
redirect_from:
 - /2024/06/18/app-file-access.html
---

Curious about what an application is doing, or where it is storing things? 

Launch `Instruments` by either opening it directly from `/Applications/Xcode.app/Contents/Applications/Instruments.app`

Or... 
  - Open `Xcode`
  - Open the `Xcode` menu, select `Open Developer Tool`
  - Click `Instruments`

Select `File Activity`, click `Choose`

In the upper left, the current machine name will be listed and next to it by default will probably say `All Processes`.

- Click `All Processes` to open the drop down
- Click `Choose Target...`
- Find the application to monitor and select it.

When ready, click the red record button.  This will launch the application (and probably prompt for administrator credentials).

Do whatever in the application, and then exit.  This will trigger Instruments to process the trace.

The `Disk Usage` tab of the navigation bar will give a complete list of files interacted with by the application.
