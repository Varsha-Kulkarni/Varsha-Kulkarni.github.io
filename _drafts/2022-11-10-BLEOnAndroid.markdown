---
layout: post
excerpt_separator: "<!--more-->"
title:  "BLE on Android"
tags: Android BLE Bluetooth
---

BLE is the Wireless communication where very less energy is consumed compared standard Bluetooth Classic. Like other mobile platform Android natively supoorts it.

I started working on BLE commnunication using healthcare devices like Weight Scale, BP Monitor, Glucose meter and Pulse oximeter. The problem I had to solve was to improve the this communication where users were facing a lot of issues pairing/syncing these peripherals with Android phone running our app. Having very brief documentation about BLE low level APIs on Android, handling the asynchronous communication, resolving errors was not an easy task. We ought to have written a layer of abstraction on top of these APIs which would handle command queues, threading and managing connection. But then we found this Library [Blessed](https://github.com/weliem/blessed-android) which helped us solve all these issues and communicate better with BLE peripherals on Android.

<!--more-->

Here I will be showing how we communicate with these peripherals and how the library solves a lot of communication problems.

Android app acts as Central or a master device which starts scanning the BLE peripherals, whereas the peripherals when put in pairing mode, start sending advertisement packets. We can specify different type of filters to scan for specific devices, for example their names, or service UUID for a specific type (Based on the service they provide, they will have Service UUID defined by [Bluetooth SIG](https://www.bluetooth.com/specifications/assigned-numbers/) or Custom ones defined by OEMs).


