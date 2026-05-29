---
title:       "Safari Web Extension in SwiftUI"
description: ""
tags:        [Xcode]
redirect_from:
  - /2024/10/01/safari-extension.html
---

The multiplatform `Safari Extension App` template is very dated.  These are the steps to make a SwiftUI version.

- TOC
{:toc}

## Steps

### Create the Main Project
Safari Extensions are delivered inside of a container app.

- Xcode > File > New > Project
- Multiplatform > App
  - Enter Product Name, etc


### Stop Targeting VisionOS
- Select the project in the Project Navigator
- Select the app in the TARGETS list and remove `Apple Vision` from the Supported Destinations


### Add the Extension Target
- Under TARGETS, click the `+` icon.
  - Under Multiplatform, select `Safari Extension App`, click Next
  - Give the extension a name, click Finish


### (Optional) Consolidate the Files
There may be no need for your extension to have a separate iOS and macOS codebase.  

If so, move everything from the "macOS (Extension)" folders into the "Shared (Extension)" folder.  The iOS and macOS `Info.plist` files are identical.

Delete the macOS and iOS Extension folders


### Cleanup the Excess Targets
There will now be four new targets in the list. In the TARGETS list:
- Select and delete the two App targets for iOS and macOS.
- Select and delete the iOS Extension target.


### Fix the Extension Target
In General:
- Select the macOS Extension and add iPhone and iPad as Supported Destinations. (You will have to enable Destibnation Support in a pop-up.)

In Build Settings:
- scroll to the Signing section, in `Code Signing Entitlements` update the path to the extensions `.entitlements` file.
- scroll to the Packaging section, in `Info.plist File` update the path to the `.plist` file.


### Fix the App Target
Switch back to the app's target in the TARGETS list.

In General, scroll down to Frameworks, Libraries, and Embedded Content:
- Click the `+` icon and select the `.appex` file.
- Click Add.


### Signing for macOS
The app will compile at this point for either iOS or macOS, but the extension will not work on macOS.

In order for the extension to be registered on macOS the app and extension must be signed.

In TARGETS (for both the app and extension), open `Signing & Capabilities` and change the Team to your team signing certificate.




