---
layout: index
components:
  - type: pageContent
  - type: readme
  - type: recentPosts
    limit: 4
    category: ':any'
    noWrapper: true
    template:
      - 'includes/postPreview_large'
next: 'gradle-refreshVersions'
---

We provide Gradle plugins and documentation to help you build good things with Gradle and Kotlin.


# de.fayard.buildSrcVersions

The Gradle plugin [de.fayard.buildSrcVersions](https://plugins.gradle.org/plugin/de.fayard.buildSrcVersions) is our first attempt at making managing Gradle dependencies easier.

It provides a task `$ ./gradlew buildSrcVersions` that generates two files `buildSrc/.../Libs.kt` and `buildSrc/.../Versions.kt` with dependencies definitions and versions that you can use wherever you want in your Gradle build.

<img src="https://github.com/jmfayard/buildSrcVersions/raw/master/doc/carbon-Libs-Versions.png"></img>

Installation:

```kotlin
// top-level "./build.gradle(.kts)"
plugins {
  id("de.fayard.buildSrcVersions") version "0.7.0"
}
```

[Read the friendly documentation](https://github.com/jmfayard/gradle-dependencies-plugins/issues/88)

# de.fayard.refreshVersions

We have exciting plans coming for the future!

The Gradle plugin [`de.fayard.refreshVersions`](https://plugins.gradle.org/plugin/de.fayard.refreshVersions) will be the core of a better solution to manage dependencies.

It provides a task `./gradlew refreshVersions` that extract the dependencies versions to a file `versions.properties`

[Read the friendly documentation](https://github.com/jmfayard/gradle-dependencies-plugins/issues/104)

![image](https://user-images.githubusercontent.com/459464/68318855-b1cc5a00-00bd-11ea-827b-cee110839337.png)

# de.fayard.dependencies

The Gradle plugin [`de.fayard.dependencies`](https://plugins.gradle.org/plugin/de.fayard.dependencies) will make it easier and much faster to add common dependencies to your build.


![Screen Shot 2019-11-06 at 5 31 38 PM](https://user-images.githubusercontent.com/459464/68317452-6e70ec00-00bb-11ea-84c4-94d3ef0fb86f.png)

# For Android devs: com.louiscad.splitties

Android devs deserve a special attention, because they have a greater need to make their builds simpler.

The Gradle plugin [`com.louiscad.splitties`]() will provide additional features for them:

- a task `./gradlew androidDoctor` will detect common Android build problems
- a task `./gradlew migrateToAndroidX` will provide a simple, fast and reliable way to migrate from the Android support libraries to AndroidX
- what else? time will tell.

# Trying it out and contributing

Don't use refreshVersions on production yet, we are moving fast and breaking things!

But do however explore the samples at https://github.com/jmfayard/gradle-dependencies-plugins

Follow this issue https://github.com/jmfayard/gradle-dependencies-plugins/issues/104

Edit the code source of this website at https://github.com/jmfayard/website-builtwithgradle

Discuss with us in Slack at [kotlinlang#gradle-refresh-versions](https://app.slack.com/client/T09229ZC6/CP5659EL9)

