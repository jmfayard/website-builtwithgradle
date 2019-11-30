---
title:  How Kotlin makes editing your Gradle build less frustrating
featuredImage: assets/media/puzzle-gradle-kotlin.png
tags:
    - android
---


Hello friends! I wrote this article those of you working with [Gradle](https://gradle.org]) and one of JetBrains IDEs (or Android Studio) and not totally happy with it. 

I would like to give you a taste of how adding a bit of [Kotlin](http://kotlinlang.org/) to your Gradle build makes it less frustrating. 

While I am at it, I will present a better way to handle dependencies management with my new project: 

{% github https://github.com/jmfayard/buildSrcVersions %}



## A confession

People who know me may be surprised that I’m about to write about and praise Gradle. I have often expressed the love-hate relationship I had with Gradle.

A lot of attention has been paid to the topic of Gradle performance (actually
of the Android build tools). Lot of work has been done there as well. See [https://guides.gradle.org/performance/](https://guides.gradle.org/performance/) and [https://developer.android.com/studio/build/optimize-your-build](https://developer.android.com/studio/build/optimize-your-build).

But I want to focus here on the second half of the problem.

> Maintenance of those build.gradle files can become quickly super tedious…

The bright side of Gradle is that it brings a lot of power and flexibility to the table, with build files that are very declarative and easy to read.

> But with great power comes the need for great tooling… or a very frustrating user experience.

And that was the dark side of Gradle. While the `build.gradle` files are easy to **read**, they are quite hard to **write**. When you need to customize them, you are mostly on your own. Usually, it doesn’t work for a while, and you don’t know why. Then at some point, you copy-paste the right solution, and it works, but you don’t really know why either.

What happened is that the dynamic nature of Groovy combined badly with all the meta-programming done in Gradle to give us poor tooling support.

Ok, so that’s the background of [Gradle's kotlin-dsl project](https://github.com/gradle/kotlin-dsl) on which
Gradle and JetBrains have been quietly working on. It’s about to reach maturity, so now is a good time to pay attention to the topic.

## How to migrate

[Migrating build logic from Groovy to Kotlin](https://guides.gradle.org/migrating-build-logic-from-groovy-to-kotlin/) mentions two possible strategies

1. _Migrating the existing syntax of your build to Kotlin, bit by bit, while retaining the structure — what we call a mechanical migration_
2. _Restructuring your build logic towards Gradle best practices and switching to Kotlin DSL as part of that effort_

For our existing projects, the latter will usually make much more sense. 

There is a story that a lot of C programmers got started doing C++ by taking this baby step.

```c
// before.c
int main() {
   /* I was doing C programming */
   printf("Hello, World!");
   return 0;
}

// after.cpp
int main() {
   // Now I'm now doing c++
   printf("Hello, World!");
   return 0;
}
```

In this spirit, I will focus on a practical first step you can take now with Gradle & Kotlin.


## Dependencies management

Gradle allows us to declare dependencies in a very terse way

```kotlin
dependencies {
  implementation("com.squareup.okio:okio:2.0.0")
  implementation("io.reactivex.rxjava2:rxjava:2.2.0")
  implementation("de.mpicbg.scicomp:krangl:0.10.3")
}
```

I have some problems with this code:

- Editing magic strings is error-prone.
- How do I centralize dependencies in a multi-module project?
- What is Krangl?
- Are there newer versions of Okio and RxJava available?

With Groovy, we can fix the centralization problems with the [dependencies.gradle pattern](https://gist.github.com/gabrielemariotti/ad6672902464ee2392d0).

Doing the same with Kotlin is quite easy:

```kotlin
// buildSrc/build.gradle.kts
plugins {
    `kotlin-dsl`
}

// buildSrc/src/main/kotlin/Libs.kt
object Libs {
    val okio = "com.squareup.okio:okio:2.0.0"
}

// some-module/build.gradle       or
// some-module/build.gradle.kts
dependencies {
   implementation(Libs.okio)
}
```

The code from the [buildSrc folder](https://docs.gradle.org/current/userguide/organizing_gradle_projects.html#sec:build_sources) is automatically available to all of your `build.gradle` or `build.gradle.kts` files.

## Laziness as a great virtue of the programmer

I had done that for a few projects, and I was happy at first. On my third project or so, it became obvious that extracting all those strings is tedious.

Also, we didn’t solve yet the problems 3) `What is Krangl?` and 4) `What new versions are available?`

There is a very nice plugin from Ben Manes, [`gradle-versions-plugin`](https://github.com/ben-manes/gradle-versions-plugin), that once applied to your Gradle project answers this. So as a lazy programmer, I wondered: could I extend it to generates exactly the Kotlin code I need?

As it turns out it is possible, so let me present my new Gradle plugin!


# Include the buildSrcVersions plugin

In your top `build.gradle` file, add:

[![pluginVersion](https://img.shields.io/maven-metadata/v/https/plugins.gradle.org/m2/de.fayard/buildSrcVersions/de.fayard.buildSrcVersions.gradle.plugin/maven-metadata.xml.svg?label=gradlePluginPortal)](https://plugins.gradle.org/plugin/de.fayard.buildSrcVersions)


```kotlin
plugins {
  id("de.fayard.buildSrcVersions") version "$VERSION"
}
```

You will find `$VERSION` at https://plugins.gradle.org/plugin/de.fayard.buildSrcVersions .

This new declarative plugins block is the preferred way to declare a plugin instead of the `buildscript {...}` syntax which provides a worse Kotlin DSL user experience.


# `$ ./gradlew buildSrcVersions`

This is the command you run the first time, and every time you added a new dependency or just want to check what versions are available.

It first executes the task `:dependencyUpdates` that connects to the internet and may take a while. This task produces a [JSON report](https://github.com/jmfayard/sample-synclibs/blob/dcfd10777dbdf6b218b9d8c9af3de6afbdf35698/report.json) with meta-data about all your dependencies, which [Kotlinpoet](https://github.com/square/kotlinpoet) helps to convert to the following code.

```kotlin
// Folder buildSrc/src/main/kotlin

object Libs {

    /** [krangl website](https://github.com/holgerbrandl/krangl) */
    const val krangl: String = "de.mpicbg.scicomp:krangl:" + Versions.krangl

    /** [okio website](https://github.com/square/okio/) */
    const val okio: String = "com.squareup.okio:okio:" + Versions.okio
  
    /** [rxjava website](https://github.com/ReactiveX/RxJava) */
    const val rxjava: String = "io.reactivex.rxjava2:rxjava:" + Versions.rxjava
}
object Versions {
  
    const val krangl: String = "0.10.3" // up-to-date
    const val okio: String = "2.0.0" // available: milestone=2.1.0
    const val rxjava: String = "2.2.0" // available: milestone=2.2.2

}
```



# IDE integration

This generated code is all we need to experience the much better IDE integration provided by Gradle’s `kotlin-dsl` in `IntelliJ IDEA` or `Android Studio ≥ 3.2`.


https://www.useloom.com/share/7edceb83fd594f319356240fcce304d5

We have now solved the problems we intended to solve

- No more magic strings, we type `Libs.<TAB>` and auto-completion does the rest.
- We can use those it in all the `build.gradle` and `build.gradle.kts` files.
- To discover what [Krangl](https://github.com/holgerbrandl/krangl) is, we press <F1> Quick documentation and click on the link to the website.
- To know which newer versions are available, we just jump from the IDE to `Libs.okio` and from there to `Versions.okio`. 2.0.0 is currently used, and a useful comment tells us that 2.1.0 is available. (The plugin never auto-apply a dependency update, that should always be the developer’s job!!).


# Give it a try

Groovy `build.gradle` and Kotlin `build.gradle.kts` files look almost similar once written. What is completely different is the writing experience of it, so I will encourage you to fire up IntelliJ IDEA or Android Studio. 

I began by confessing that I hated the **black magic** aspect of Gradle. Opening the black box with Kotlin has been super useful. I realized that Gradle is simply a good, useful, well-documented API. An API that you can work with using your developer tools and skills like with any other Java API. An API whose goodness had been hidden from us. I plan to go deeper in following articles if there is enough interest.

## Links

My plugin

{% github https://github.com/jmfayard/buildSrcVersions %}


The gradle/kotlin-dsl samples

{% github https://github.com/gradle/kotlin-dsl-samples %}