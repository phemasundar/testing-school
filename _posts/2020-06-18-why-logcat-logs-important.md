---
layout: post
title:  "Importance of LogCat logs in Android automation test execution"
author: hema
categories: [ appium ]
tags: [appium]
image: assets/images/logs.png
description: "Importance of LogCat logs in Android automation test execution"
featured: true
hidden: true
---

In any software or application logs play a vital role. When something goes wrong in the application, logs are the first place one will check to find out what exactly happened.
Generating proper log messages will reduce the time to fix any issue. This will be of much use in case of a production bug. This applies not just for development projects even for Test automation projects as well.

#### Different Logs available for Appium - Android

In the case of UI test automation, whenever any test case fails first thing one will check is the screenshot which is a log of Image type. Contrary to most people's thinking, Logs are not just text messages. Screenshots, Screen records as well come under logs only. But these logs (screenshots, screen records, or log messages) are custom logs those generated as part of our code/framework.

By default Appium provides the below log types for Android:
* logcat
* bugreport  
* server
* client

In this article, we are going to discuss the importance of LogCat logs.

#### How to get LogCat logs
Use the below code to get the logcat logs and write them into a text file:
```java
public class TestHooks {
    public void getLogcatLogs(AndroidDriver driver, String logFilePath) throws IOException {
        if (TestData.getConfigData().getLogCatLogs()) {
            String totalLogs = "";

            LogEntries logEntries = driver.manage().logs().get("logcat");
            List<LogEntry> all = logEntries.getAll();
            List<String> logsInStringList = all.stream().map(LogEntry::toString).collect(Collectors.toList());

            totalLogs = String.join("\n", logsInStringList);

            Files.write(Paths.get(logFilePath), totalLogs.getBytes());
        }
    }
}
```

Please make sure to use a minimum of Java 8 for compiling the above code successfully.

#### Why LogCat logs are the Important?
Whenever any test case fails most of the time just by seeing the screenshot of the mobile screen only we can identify the root cause for failure. But in some cases like application crashes, just by seeing the screenshot one can not say the root cause to fix the bug. And the worst part is 90% of the cases application crashes happen randomly (Won't happen every time you execute the test case).
Application crash might be triggered with some other third-party app in mobile or some issue with the backend API calls etc. In cases similar to this, logcat logs can be of great use for the dev team to find the root cause of the issue. Thus reducing the time of bug fix which ultimately helps in faster deliveries.
 
#### How to fix discarding LogCat log entries
If you feel that you are not getting all the logcat logs for the current test, there might be a problem with the log buffer of the device. Please follow [this](../update-logcat-buffersize) article to fix the issue.

I hope the above approach helps. Please let me know in the comments if any other ways of doing this.
