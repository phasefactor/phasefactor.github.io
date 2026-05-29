---
title:       "Deploying Simple AdBlocker on MacOS/iOS"
description: "How to deploy the SimpleBlock extension."
tags:        [Xcode, Security]
modified:    2026-05-14
redirect_from:
  - /2024/01/17/safari-ad-block.html
---

Commercial ad blocking extensions make their money by collecting data on their user's browsing.  Very few open source projects have extensions for Safari, so we roll [our own](https://github.com/phasefactor/AdBlocker/)...

***NOTE:*** I am leaving this here for historical purposes, but the main problem it addresses is no longer an issue.  [uBlock Origin](https://ublockorigin.com) is now available for MacOS, iOS, and iPadOS in [the App Store](https://apps.apple.com/us/app/ublock-origin-lite/id6745342698).  It is much more comprehensive and capable than the simple extension described here.

- TOC
{:toc}

---

## Prerequisites
Apple's [Xcode IDE](https://apps.apple.com/us/app/xcode/id497799835) is necessary to compile and deploy the extension.  It is free.

Either a paid Apple Developer Account is required, or a [locally issued signing certificate]({% link _notes/2024-01-18-xcode-without-developer-account.md %}).

### Acquire the Source Code
- Open Xcode
- On the startup screen, click **Clone Git Repository...**
- In the text box at the top of the screen enter the repository URL:
    `https://github.com/phasefactor/AdBlocker.git`
- Click **Clone**.
- Select a location for Xcode to download the project and click **Clone**.

### Select Team
- In the left side navigation pane, click on the root **AdBlocker** node of the tree.
- Open the **Signing & Capabilities** tab in the center pane.
- For BOTH the **AdBlocker** and **AdBlocker Extension** targets:
  - In the **Signing** section, click the **Team** dropdown.
  - Select the account you added earlier (or [add the account]({% link _notes/2024-01-18-xcode-without-developer-account.md %})).

---



## Deploy to MacOS

### Build and Deploy the App
- Select "My Mac" as the deployment destination:
  - In the top menu bar, click **Product**.
  - Click **Destination**.
  - Click **My Mac**.
- Build and run.
  - In the top menu bar, click **Product**.
  - Click **Run**

This should build and run the AdBlocker app, which contains the extension and registers it with Safari.  The extension must still be turned on in Safari.

### Enable Unsigned Safari Extensions
- Open Safari and open Settings by either:
  - Click **Safari** in the top menu bar, then click **Settings...**.
  - Or press **Command - ,** to open Settings directly.
- Open the **Advanced** tab.
- Click the checkbox next to **Show features for web developers**.
- Open the newly revealed **Developer** tab.
- Click the checkbox next to **Allow unsigned extensions**.


### Enable the Extension in Safari
- Open Safari and open Settings by either:
  - Click **Safari** in the top menu bar, then click **Settings...**.
  - Or press **Command - ,** to open Settings directly.
- Open the **Extensions** tab.
- Click the checkbox next to **AdBlocker Extension**.
- Enable to extension in private browsing (if you want):
  - Click on **AdBlocker Extension**.
  - Click the checkbox next to **Allow in Private Browsing**. 

---



## Deploy to iOS

### Build and Deploy the App
- Connect your iOS device to your development computer.
  - You may be asked whether to trust the computer.
- Select your iOS device as the deployment destination:
  - In the top menu bar, click **Product**.
  - Click **Destination**.
  - Click on your iOS device's name.
- Build and run.
  - In the top menu bar, click **Product**.
  - Click **Run**

If this is the first thing deployed to the iOS device with this signing certificate, then the certificate must be manually added to the trusted list.

This should build and run the AdBlocker app, which contains the extension and registers it with Safari.  The extension must still be turned on in Safari.

### Trusting the Signing Certificate
Once you deploy the app to your iOS device:
- Open **Settings**.
- Click **General**.
- Click **VPN & Device Management**.
- Select the certificate and trust it.

### Enable the Extension in Safari
- Open **Settings**.
- Click **Safari**.
- Click **Extensions**.
- Click **AdBlocker Extension** in the **Allow these Extensions** list.
- Click **Allow Extension** and (if you want) click **Allow in Private Browsing**.

  







