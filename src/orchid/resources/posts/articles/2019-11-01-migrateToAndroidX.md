---
title: gradle migrateToAndroidX - beta testers needed
featuredImage: assets/media/pic03.jpg
tags:
    - android
---

Hello Android devs,

I am looking for beta testers / early adopters for a new project I have been working on together with LouisCAD.

We want to provide a **reliable, simple, fun way to migrate an old Android project using the support libraries to AndroidX**.

## Why?

There are [hundred of thousands of projects that are still using "com.android.support:appcompat-v7" and friends](https://github.com/search?q=com.android.support%3Aappcompat-v7+filename%3Abuild.gradle&type=Code&utf8=%E2%9C%93) which are **not supported** anymore.

The Android team asked everyone to migrate to AndroidX instead, but in our opinion they have fucked up the migration story part of it. Which creates a big mess for app developers and library authors.

In theory, all you have to do is to use the menu entry:

   **Refactor > Migrate to AndroidX**

In practice, our experience was waiting minutes with an unresponsive IDE, then giving up with no other choice than force closing Android Studio, and finally getting a broken project, with some dangling fully qualified references (instead of proper import replacement). 


## What does it look like?

```bash
$ ./gradlew migrateToAndroidX

## Checking that you use compileSdkVersion 28
✔ 🆗 You are using compileSdkVersion 28

## Migrating classes from support libraries to AndroidX.
✔ 🆗 Parsing file androidx-class-mapping.csv
✔ 🆗 Modules: [Application]
✔ 🆗 Found 18 source files that may need migration
✔ 🆗 File androidx-class-mapping.csv parsed correctly
✔ 🆗 Starting batch migration...
Migrating file "CameraActivity.java" … overwriting file…  Done. ✔🆗
Migrating file "Camera2BasicFragment.java" … overwriting file…  Done. ✔🆗
Migrating file "build.gradle" …
✔ 🆗 2 source files (kt,java,xml) have been migrated (16 didn't need it).
✔ 🆗 0 gradle files have been migrated (1 didn't need it).

## Detecting Gradle properties
✔ 🆗 gradle.properties already contains [android.useAndroidX, android.enableJetifier]

## Your turn: add the AndroidX libraries to app/build.gradle

// app/build.gradle
dependencies {
    implementation(AndroidX.legacy.supportV4)
    implementation(AndroidX.legacy.supportV13)
    implementation(AndroidX.cardView)
    implementation(AndroidX.appCompat)
}
```

## I am in! What do you need?

To make the project **reliable**, we need people willing to beta test it once we make it available. 

You may have a project that needs to be migrated, or that you have migrated already but you can find the commit before in the commit history.

If you are interested, please contact me by email
