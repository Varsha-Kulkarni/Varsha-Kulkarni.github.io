---
layout: post
title:  "Bluetooth Permissions on Android 12"
tags: Android12 targetSdk Bluetooth BLE run-time permissions location fine_location_permission
---

Beginning with Android 12, new run-time permissions are added for accessing nearby Bluetooth devices. Without this permission any APIs related to Bluetooth devices access will result into Fatal Exception: 

```
java.lang.SecurityException
Need android.permission.BLUETOOTH_CONNECT permission ...
```
You can use permissions for Bluetooth as follows for targetSdk set to 31 and minSdk to 23


```
 <uses-permission
        android:name="android.permission.BLUETOOTH"
        android:maxSdkVersion="30" />
    <uses-permission
        android:name="android.permission.BLUETOOTH_ADMIN"
        android:maxSdkVersion="30" />
    <uses-permission android:name="android.permission.BLUETOOTH_CONNECT" />
    <uses-permission
        android:name="android.permission.BLUETOOTH_SCAN"
        android:usesPermissionFlags="neverForLocation"
        tools:targetApi="s" />
    <uses-permission android:name="android.permission.ACCESS_FINE_LOCATION" />
    <uses-permission android:name="android.permission.ACCESS_COARSE_LOCATION" />
```
Please note that if your app makes the current device discoverable to other Bluetooth devices, that's if the device which is running the app as master and advertising the data, then declare the BLUETOOTH_ADVERTISE permission.

If you are looking for other Bluetooth devices, then declare BLUETOOTH_SCAN.

If your app is doing any operations on Bluetooth devices like pairing, checking whether a peripheral is already connected, or connecting or communicate in any way, declare BLUETOOTH_CONNECT. 

Legacy Bluetooth permissions are required for older OS versions below Android 31.

If in any way if your app is trying to derive user's location using Scan results, then only request for Location permissions. else `usesPermissionFlags` can be set to `neverForLocation`

Then while requesting permissions, based on OS version, use the required run-time permission as follows:

```
  private String[] getRequiredPermissions() {
        int targetSdkVersion = getApplicationInfo().targetSdkVersion;
        if (Build.VERSION.SDK_INT >= Build.VERSION_CODES.S && targetSdkVersion >= Build.VERSION_CODES.S) {
            return new String[]{Manifest.permission.BLUETOOTH_SCAN, Manifest.permission.BLUETOOTH_CONNECT};
        } else if (Build.VERSION.SDK_INT >= Build.VERSION_CODES.Q && targetSdkVersion >= Build.VERSION_CODES.Q) {
            return new String[]{Manifest.permission.ACCESS_FINE_LOCATION};
        } else return new String[]{Manifest.permission.ACCESS_COARSE_LOCATION};
    }
```

