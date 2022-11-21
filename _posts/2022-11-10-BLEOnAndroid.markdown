---
layout: post
excerpt_separator: "<!--more-->"
title:  "BLE on Android"
tags: Android BLE Bluetooth
---

BLE is the Wireless communication where very less energy is consumed compared to standard Bluetooth Classic. Like other mobile platforms Android natively supports it.

I started working on BLE commnunication using healthcare devices like Weight Scale, BP Monitor, Glucose meter and Pulse oximeter. The problem I had to solve was to improve this communication where users were facing a lot of issues pairing/syncing these peripherals with Android phone running our app. Having very brief documentation about BLE low level APIs on Android, handling the asynchronous communication, resolving errors was not an easy task. We ought to have written a layer of abstraction on top of these APIs which would handle command queues, threading and managing connection. But then we found this Library [Blessed](https://github.com/weliem/blessed-android) which helped us solve all these issues and communicate better with BLE peripherals on Android.

<!--more-->

Here I will be showing how we communicate with these peripherals and how the library solves a lot of communication problems.

Android app acts as Central or a master device which starts scanning the BLE peripherals, whereas the peripherals when put in pairing mode, start sending advertisement packets. We can specify different type of filters to scan for specific devices, for example their names, or service UUID for a specific type (Based on the service they provide, they will have Service UUID defined by [Bluetooth SIG](https://www.bluetooth.com/specifications/assigned-numbers/) or Custom ones defined by OEMs).

#### Scanning
Here I have used SERVICE_UUID of Glucose Monitor (00001808-0000-1000-8000-00805f9b34fb) for scanning.

```
central = BluetoothCentralManager(context, bluetoothCentralManagerCallback, Handler(Looper.getMainLooper()))
central.scanForPeripheralsWithServices(arrayOf(GLUCOSE_SERVICE_UUID))
```

#### Connecting

When peripheral with the specified Service UUID is discovered, the registered callback gets called, where we can continue to connect with the desired peripheral as follows:

```
var bluetoothCentralManagerCallback: BluetoothCentralManagerCallback = object : BluetoothCentralManagerCallback() {
        override fun onDiscoveredPeripheral(peripheral: BluetoothPeripheral, scanResult: ScanResult) {
            central.stopScan()
            if (peripheral.name?.contains(CONTOUR_NEXT_ONE) == true) {
                central.connectPeripheral(peripheral, peripheralCallback)
            }
        }
        ...
 }
```
On successful connection `onConnectedPeripheral` gets called, but the connection with peripheral may fail due to various reasons such as timeout, since the connection process may return within 5-6 seconds (as we noticed in our app), which may require more time needing to retry connecting. This is how we achieved it:

```
 override fun onConnectionFailed(peripheral: BluetoothPeripheral, status: HciStatus) {
     if (status == HciStatus.ERROR || status == HciStatus.CONNECTION_FAILED_ESTABLISHMENT){
           if (tryCounter < NUMBER_OF_CONNECTION_TRIES)
                central.connectPeripheral(peripheral, peripheralCallback)
                if (tryCounter == BlessedLibConstant.NUMBER_OF_CONNECTION_TRIES) {
                   //notify UI about the failure
                }
            }
      }
 }
```
Once the peripheral is connected, the Android device and the BLE peripheral may exchange keys and store the information to make the connection secure and trusted which is called as **Bonding**, the method `onBondingSucceeded` gets called. UI can be updated to show the user that pairing is completed either in this method or in `onConnectedPeripheral` based on which one gets called first. We used a flag to identify whether UI has been already updated or not. There are peripherals which may fail to bond, like the Weight Scale, which we were using in our project with custom Service UUID, failed to bond on Android 11+.

#### Syncing the data

The data from the peripherals is received in the form of peripheral CHARACTERISTICS. Once connected with the peripheral, it returns the services it can offer. The following callback gets called:

```
  var peripheralCallback: BluetoothPeripheralCallback = object : BluetoothPeripheralCallback() {
      override fun onServicesDiscovered(peripheral: BluetoothPeripheral) {
          super.onServicesDiscovered(peripheral)
          peripheral.setNotify(GLUCOSE_SERVICE_UUID, BlessedLibConstant.Glucose.GLUCOSE_MEASUREMENT_CHARACTERISTIC_UUID, true)
      }
  }
```
In the above callback, we call  `setNotify` to notify the android app if any data is received from the peripheral. When the peripheral sends the data, `onCharacteristicUpdate` gets called, where we parse the data.

```
 override fun onCharacteristicUpdate(peripheral: BluetoothPeripheral,
                                            value: ByteArray,
                                            characteristic: BluetoothGattCharacteristic,
                                            status: GattStatus) {
        super.onCharacteristicUpdate(peripheral, value, characteristic, status)
        if (status != GattStatus.SUCCESS) return
        if (characteristic.uuid == BlessedLibConstant.Glucose.GLUCOSE_MEASUREMENT_CHARACTERISTIC_UUID) {
            //parse the data received
        }
 }
```
Blessed Library provides `BluetoothByteParser` utility for parsing the data. Also a detailed [blog](https://medium.com/@martijn.van.welie/making-android-ble-work-part-1-a736dcd53b02) series is written by the author of [Blessed Library](https://github.com/weliem/blessed-android) explaining how to make BLE work on Android. The library also includes a example app which was a great help while working on this project. With the above implemetation we updated our app to resolve most of the issues patients were facing, who had to manually enter their Vitals. Hope this helps other developers too:)
