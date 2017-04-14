---
layout: post
title:  "Configuring Java Module with Dagger2 and Kotlin in Android Studio "
date:   2017-04-14 01:23:45
categories: android
tags: [kotlin, dagger2, android studio, java library, module, java]
---

Several weeks ago I started to work on moving all code related to communication with APIs to a separate module.

The reason behind that was to decouple UI and Api layers and in future use Api layer beyond Android app. Especially with Kotlin Native I guess it would be possible to use shared components between Android and iOS applications.


## It all started with a module

Right click on a project in Android Studio, New - Module - Java Library:
![Android Studio New Module](/images/android_studio_new_module.png)

Boom...I got a new module.

## gradle :api:build

First compilation, first error:
![Compilation error](/images/compilation_error.png)

Of course, I use Kotlin why I even need Java 8?

Thanks to the error message. I need to add those two lines in module's build.gradle file:
```groovy
sourceCompatibility = 1.7
targetCompatibility = 1.7
```

And now it compiles. Perfect!

## Useful library

I use Dagger 2 as DI library to easily manage my dependencies. I added few lines in build.gradle:
```groovy
compile "com.google.dagger:dagger:2.x"
kapt "com.google.dagger:dagger-compiler:2.x"

```

Modules and components were also added according to my requirements.

Tried to compile...Build Successful. Great!

## One class to rule them all

Since I marked all classes and interfaces as internal I created a class as main entry point for my new module.
Inside the class I need to inject from a Component which I created before. But I wasn't able to see any generated dagger components

Kapt generates all Dagger related classes under _build/generated/source/kapt_ folder. But entire build folder was marked as excluded in Android Studio. Tried to solve it by setting sourceSets :
```groovy
sourceSets.main.java.srcDir file("$buildDir/generated/source/kapt")

```
It didn't help.

## Gradle idea plugin
I looked at iml file for my module and found that build folder was under _\<excludeFolder>_. Ok deleted that.
After the sync build folder appeared under _\<excludeFolder>_ again. Somehow from the Gradle I should say to Android Studio that build folder should not be excluded.

That's exactly what idea plugin can do:
```groovy
apply plugin: 'idea'

idea {
    module {
        excludeDirs -= buildDir
    }
}
```

Now everything is working as suppose to be.
<br><br>

You can take a look at [my project](https://github.com/rustamgaifullin/TranslateIt) to see how exactly it was done.
