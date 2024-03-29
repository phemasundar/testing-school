---
layout: post
title:  "Take screenshot when Selenium/Appium fails using Java"
author: hema
categories: [ java, appium, selenium ]
tags: [java, selenium]
image: assets/images/screenshot.png
description: "Take screenshot when Selenium/Appium fails using Java"
featured: true
hidden: true
---

When it comes to debugging the failed test cases in selenium or appium having a screenshot of the web or mobile application can be very much handy.
99% of the time one can identify the reason for automated test case failure just by seeing the screenshot at the time of failure without having to go through the logs dump.

In this article we are going to discuss the following:
* How to take screenshots using selenium
* situations where selenium fails to take a screenshot
* How to take a screenshot with only java code without using selenium

#### Take a screenshot using selenium

Below code snippet, can be used to generate the screenshot using selenium.

```java
public class Screenshot {
    public static void getScreenshot(WebDriver driver, String fileName) {
        try {
            File screenshotFileObj = ((TakesScreenshot)driver).getScreenshotAs(OutputType.FILE);
            FileUtils.copyFile(screenshotFileObj, new File(fileName));

        } catch (Exception e) {
            //TODO: Handle the exception
        }
    }
}
```

For more details on different ways of taking screenshot using selenium please follow the [link]()

#### Why do we need to take a screenshot using java?

Most of the time taking screenshot using selenium are more than enough. But in some cases selenium won't be able to take the screenshot.
* When browser window went not responding state
* When the driver object got killed because of some issue
* In case of Mobile automation test cases, when the device rejects appium driver access because of some administrator app takes control of the device

In most companies, mobile devices provided for executing automation test cases are managed by the company itself. And they can access the device by using some schedulers or using some backend users. For example, when the test cases are running on the mobile device, if the auto-update process started, it will restrict the access to Appium driver. In this case, while trying to take screenshot Appium will throw WebDriverException.

In the above-mentioned scenarios, as we won't be able to take screenshots using Selenium, it is a better approach to take the entire desktop/system screenshot using java.  

#### Taking a screenshot using java?

The below approach uses java.awt.Robot class to capture the screen pixels and returns a BufferedImage. 

Java.awt.Robot class is used to take control of mouse and keyboard. Once you get the control, you can do any type of operation related to mouse and keyboard through your java code.

Copy and paste the following code in your Java class and invoke the method captureScreen() with the file name as argument. The screenshot will be stored in the file that you specified in the argument.

```html
import java.awt.Dimension;
import java.awt.Rectangle;
import java.awt.Robot;
import java.awt.Toolkit;
import java.awt.image.BufferedImage;
import javax.imageio.ImageIO;
import java.io.File;

public void captureScreen(String fileName) throws Exception {

   Dimension screenSize = Toolkit.getDefaultToolkit().getScreenSize();
   Rectangle screenRectangle = new Rectangle(screenSize);

   Robot robot = new Robot();

   BufferedImage image = robot.createScreenCapture(screenRectangle);
   ImageIO.write(image, "png", new File(fileName));

}
```

#### Integrating the Java screenshot with Selenium screenshot code

It is always recommended to use Selenium for taking a screenshot. In case selenium is not able to take the screenshot then we should take the screenshot using java or any other programming language we are using to write selenium code.

Please take a look at the below code:

```html
    public static void getScreenshot(WebDriver driver, String fileName) {
        try {
            File screenshotFileObj = ((TakesScreenshot)driver).getScreenshotAs(OutputType.FILE);
            FileUtils.copyFile(screenshotFileObj, new File(fileName));

        } catch (Exception e) {
            try {
                Dimension screenSize = Toolkit.getDefaultToolkit().getScreenSize();
                Rectangle screenRectangle = new Rectangle(screenSize);

                Robot robot = new Robot();

                BufferedImage image = robot.createScreenCapture(screenRectangle);
                ImageIO.write(image, "png", new File(fileName));
                
            }catch (IOException | AWTException e1) {
                //TODO: log the exception
            }
        }
    }
```

I hope the above approach helps. Please let me know in the comments if any other ways of doing this.
