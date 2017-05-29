---
layout: post
title:  "Resolving a problem of mocking final classes in Kotlin and running tests in Java Modules from Android Studio. "
date:   2017-05-29 01:23:45
categories: android, kotlin
tags: [kotlin, android, unit testing, final, mock, mockito2, android studio, java library]
---

In Kotlin classes are final by default and to be able to mock them you have to make additional configuration.
You can find thousands of tutorials how to mock final classes in Kotlin and those all work.
Unless you use Android Studio 2.3.x and divided codebase by several modules.

## Mockito2

I use mockito library to mock classes and mockito has official way to provide mocking for final classes.
To do that you need to create a file in *test/resources/mockito-extensions* directory with name - *org.mockito.plugins.MockMaker*.
![mock macker file](/images/mockito_finals.png)

Inside the file put one line:
```
mock-maker-inline
```
And that simple configuration give you ability to mock final classes.


## Mock finals in modules  
The problem occurred when I created additional Java Module and ran a test from Android Studio.
For some reason mockito was not able to mock final classes. By accident I discovered that mockito has a different library called Mockito Inline started from 2.7.2 version.
I added that library to my new module and mocking final classes worked again.
```groovy
testCompile "org.mockito:mockito-core:$mockito_version"
testCompile "org.mockito:mockito-inline:$mockito_version"
```
