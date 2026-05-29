---
title:       "Using Xcode Without an Apple Developer Account"
description: ""
tags:        [Xcode]
excerpt_separator: <!--more-->
redirect_from:
  - /2024/01/18/xcode-without-developer-account.html
---

Xcode is Apple's free development environment, [available from the App Store](https://apps.apple.com/us/app/xcode/id497799835). 

Apps can be deployed locally with a free Apple ID and a locally issued signing certificate, but putting apps in the App Store requires a paid Apple Developer account.

<!--more-->

To generate a signing certificate, do the following:
- Launch **Xcode**.
- Open Xcode Settings by either:
  - Click **Xcode** in the top menu bar, then click **Settings...**.
  - Or press **Command - ,** to open Settings directly.
- Open the **Accounts** tab.
- Click the **+** button on the bottom left.
- Select **Apple ID** and click **Continue**.
  - Log in with your Apple ID.
- Click **Manage Certificates...**
- If there is no Apple Development Certificate listed click the **+** button.
  - Select **Apple Development**
- Click **Done** and close the Settings window.

