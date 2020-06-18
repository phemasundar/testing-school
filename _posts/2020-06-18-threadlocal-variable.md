---
layout: post
title:  "Importance of ThreadLocal in parallel execution of test cases"
author: hema
categories: [ java, tutorial, selenium ]
tags: [java, selenium]
image: assets/images/multithreading.jpeg
description: "Importance of ThreadLocal in parallel execution of test cases"
featured: true
hidden: true
---

Executing test cases in parallel sounds interesting and when we want to implement, it looks very easy as we just need to update some settings in the TestNG XML file. But parallel or multi-thread executing is a lot more complex than it seems from outside.
If something goes wrong in the multi-threaded environment, it is very difficult to debug or identify the issue.

The main reason for the complexity of the parallel execution of test cases is not in configuring the threads but in blocking data to leak in between threads. Below are the common challenges:
* Keeping the variables local to the current thread. It means two different threads should not be able to read/write a variable.
* Sharing the data in between the threads without multiple threads being able to write/override a variable at the same time.
* Multiple threads should not be able to call the same method having write operations at the same time

In the current article, we are going to discuss how to implement the first point.
 
#### What is ThreadLocal variable

ThreadLocal is a type of variable introduced in java 1.2. It will make sure the variable is local to the current thread only. Whenever we want to use static variables to be used as local to a current thread, we can define it as ThreadLocal Type.

#### How ThreadLocal works internally

Internally ThreadLocal works on HashMap. When you initialize a ThreadLocal variable, internally java will create a HashMap with key as Thread ID/Name and value as ThreadLocal's variable value for that thread.
So when we try to get the value of the ThreadLocal variable, it will return the value of the current thread. Once the thread is dead, the ThreadLocal's current thread value is marked for garbage collecting. 

#### Will ThreadLocal be a performance hit?

As mentioned in the above section, ThreadLocal internally works on HashMap having an individual entry for each thread. So let us say if the Object is heavy and we have more threads in parallel then it can be a performance hit.

So it is always recommended to have local non-static variables to keep any variable as local to the current thread. When one can not maintain that the next option is to go ThreadLocal variable type.
 
#### How to Use ThreadLocal?

Below is the concise information on how to use a ThreadLocal variable. For detailed use of Thread local please follow the link

##### Create a vriable
```java
ThreadLocal myThreadLocal = new ThreadLocal<String>();
```
##### assign value to ThreadLocal
```java
myThreadLocal.set("Hello ThreadLocal");
```
##### get ThreadLocal value
```java
String threadLocalValue = myThreadLocal.get();
```

#### Problems where ThreadLocal is Useful

Below am going to discuss a problem that I faced personally where using ThreadLocal can save the day.

##### Problem:

I am trying to execute a test in multiple browsers, in parallel using TestNG.
But it is always considering the second-mentioned browsers only.

TestNG.xml

```xml
<?xml version="1.0" encoding="UTF-8"?>
<suite name="Parallel Tests" verbose="1" thread-count="10" parallel="tests">
    <tests> 
        <test name="Test1">
            <parameters>
                <parameter name="platform" value="Windows" />
                <parameter name="browser" value="chrome" />
                <parameter name="version" value="8" />
            </parameters>
            <classes>
                <class name="com.tribune.uiautomation.testscripts.TestEngine"/>
            </classes>
        </test> <!-- Test -->

        <test name="Test2">
            <parameters>
                <parameter name="platform" value="Windows" />
                <parameter name="browser" value="firefox" />
                <parameter name="version" value="8" />
            </parameters>
            <classes>
                <class name="com.tribune.uiautomation.testscripts.TestEngine"/>
            </classes>
        </test> <!-- Test -->

    </tests>    
</suite> <!-- Suite -->
```

TestEngine class:

```java
public class testEngine {
    @BeforeTest
    @Parameters("browser")
    public void beforeTest(String browser){
        Property.BrowserName = browser;
        System.out.println("BrowserName got from XML is "+Property.BrowserName);
    }

    @Test
    public void executeTest() {
    //code for launching browser & performing tests...
    }
}
```

```log
Console output is similar to:
log4j:WARN [] should be System.out or System.err.
log4j:WARN Using previously set target, System.out by default.
[TestNG] Running:
  D:\Selenium_Projects\WorkSpace_Personal\TribuneWebScriptLess\TestNG.xml

BrowserName got from XML is chrome
BrowserName got from XML is firefox
```

After the second log statement of firefox, it is considering only firefox browser only (i.e. both the tests mentioned in TestNG.xml are running in firefox only)

##### Reason:

In the framework, the WebDriver object is a static variable. In Java, Static variables are used to share the data between the threads. So when the second thread assigns a value to the static variable it overrides the value set by the first thread. This causes the problem.

##### Solution using ThreadLocal:

We just need to change the WebDriver object type to ThreadLocal. That's it. The problem will solve automatically. 


I hope the above approach helps. Please let me know in the comments if any other ways of doing this.
