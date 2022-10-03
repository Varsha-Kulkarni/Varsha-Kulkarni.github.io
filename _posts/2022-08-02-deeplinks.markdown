---
layout: post
excerpt_separator: "<!--more-->"
title:  "Applinks on Android 12"
tags: Android targetSdk applinks deeplinks assetlinks
---

If you are trying to access your app from web, trying to click on a link which automatically opens the app without ambiguity, you need app links to be created.
Before Android 12 (API 31), adding intent filters under the `Activity` which needs to open on link clicking would open the app, may be with some amibguity.
 But if targetSdk is set to 31, there are mandatory steps need to be followed, otherwise the link can open only on the browser.

Follow the step from Android documentation [here](https://developer.android.com/training/app-links) and [here](https://developer.android.com/studio/write/app-link-indexing)

Things to note here:
- If you are using multple domains ensure that all the domains have hosted correct assetlinks.json.
- If verification of under one domain fails, then entire verification fails.
> Note: On Android 11 (API level 30) and lower, the system establishes your app as the default handler for the specified URL patterns only if it finds a matching Digital Asset Links file for all hosts in the manifest.

- Install the app through adb, or run the following command, open logcat and see the verification process working. 

```
$ adb shell pm verify-app-links --re-verify <PACKAGE_NAME>
```
Filter with **IntentFilterIntentOp** 

```
I/IntentFilterIntentOp: Verification 23 complete. Success:true. Failed hosts:. [CONTEXT service_id=244 ]
```

- Make sure the sha256_cert_fingerprints are correct.
sha256_cert_fingerprints of debug & release keystore are different.
If only release fingerprints are added on assetlinks.json, the verification doesn't work on debug version. so adb shell pm get-app-links <PACKAGE_NAME> returns legacy_failure.
It will work if both fingerprints are added(debug & release) on assetlinks.json

- Ensure there are no syntax errors or unnecessary spaces or special characters added to assetlinks.json
It is better to use App Links Assistant tool provided by Android Studio to create this file instead of manually creating.
For testing the assetlinks.json statements are correct, use [this](https://developers.google.com/digital-asset-links/tools/generator)



If verifying manually, the typical output expected:
```
$ adb shell pm get-app-links --user cur
 com.example
  com.example:
    ID: 90a79cb0-b63c-4ffa-aeb7-b3ad109a7272
    Signatures: [ED:64:B4:70:53:31:7E:C0:F4:99:9B:F9:49:AA:D1:0F:1D:63:75:B1:CB:03:DD:5D:D7:BC:E3:E2:7D:2E:00:29]
    Domain verification state:
      api.prod.example.com: legacy_failure
      api.test.example.com: legacy_failure
      api.dev.example.com: legacy_failure
    User 0:
      Verification link handling allowed: true
      Selection state:
        Disabled:
          api.prod.example.com
          api.test.example.com
          api.dev.example.com
```

```
$ adb shell pm set-app-links --package com.example 0 all
```

```
$ adb shell pm get-app-links --user cur
 com.example
  com.example:
    ID: 90a79cb0-b63c-4ffa-aeb7-b3ad109a7272
    Signatures: [ED:64:B4:70:53:31:7E:C0:F4:99:9B:F9:49:AA:D1:0F:1D:63:75:B1:CB:03:DD:5D:D7:BC:E3:E2:7D:2E:00:29]
    Domain verification state:
      api.prod.example.com: none
      api.test.example.com: none
      api.dev.example.com: none
    User 0:
      Verification link handling allowed: true
      Selection state:
        Disabled:
          api.prod.example.com
          api.test.example.com
          api.dev.example.com
		  
```

Give some time for it to complete the verification process

$ adb shell pm get-app-links --user cur
 com.example
  com.example:
    ID: 4f4f913e-c4be-4b03-b782-9d6b9233396f
    Signatures: [ED:64:B4:70:53:31:7E:C0:F4:99:9B:F9:49:AA:D1:0F:1D:63:75:B1:CB:03:DD:5D:D7:BC:E3:E2:7D:2E:00:29]
    Domain verification state:
      api.prod.example.com: verified
      api.test.example.com: verified
      api.dev.example.com: verified
    User 0:
      Verification link handling allowed: true
      Selection state:
        Disabled:
          api.prod.example.com
          api.test.example.com
          api.dev.example.com


I have spent hours debugging applinks which failed to open the app, instead it would try it on browser on Android 12. There are some blog posts around this which helped me troubleshooting.

https://zarah.dev/2022/02/08/android12-deeplinks.html
https://handstandsam.com/2017/07/11/troubleshooting-auto-verification-of-seamless-android-app-deep-linking/

With the above steps followed, it will also resolve the ambiguity on older OS versions, open the exact intended app.
