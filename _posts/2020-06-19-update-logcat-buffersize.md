---
layout: post
title:  "Update LogCat buffer size"
author: hema
categories: [ Appium ]
tags: [Logs, Android]
image: assets/images/logs.png
description: "Importance of ThreadLocal in parallel execution of test cases"
featured: true
hidden: true
---

LogCat is a command-line tool provided within android SDK tools that dumps a log of system messages, including stack traces when the device throws an error and messages that you have written in your dev app code with the Log class.

As it dumps logs from across the apps in the Android device, it will be useful to check for any issues that occurred in the Application Under Test because of any other third party application.

#### Why is it essential to update the LogCat Buffer size?

> By default, any Android device comes with 256K of buffer size. In other words, the maximum size of the logcat logs that one can read is 256K which in most cases is not sufficient.

As LogCat dumps the logs across the apps in device, within no time it will reach the 256K mark. So when we want to get the LogCat log for the entire automation test case we need to increase the LogCat buffer size according to our necessity.

#### How to get LogCat logs programmatically in Appium?
To understand on how to get logs in Appium follow [here](../why-logcat-logs-important/)

#### How to update Logger Buffer size?

As a prerequisite one should **enable Developer option** in the device. Follow [here]() to find out how to enable Developer options in Android.

* In Android device, Go to the Settings app
* Settings app, Swipe till the end to find **Developer options**
* Tap on Developer Options entry followed by swipe down slowly to find option **Logger buffer sizes** and tap on that.
* Update the Buffer size as per your necessity

#### How to update Logger Buffer size from command line?

_coming soon..._

I hope the above approach helps. Please let me know in the comments if any other ways of doing this.
