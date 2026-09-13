---
title: Android
weight: 10
pre: "<b>3. </b>"
chapter: true
copyright: JitPack 2026
---

# Publish an Android library 

To publish your Android library on JitPack you just need a working build file in your Git repository.

The Android SDK is available in the build environment and the `ANDROID_HOME` variable is already set when the build starts.

> **Important:** Modern Android Gradle Plugin versions (AGP 8.0+) require **Java 17**. If your project uses AGP 8+, you must add a `jitpack.yml` file to the root of your repository:
> ```yml
> jdk:
>   - openjdk17
> ```

## Gradle

To build and publish your library on JitPack you need the `maven-publish` plugin as explained in [the Android documentation](https://developer.android.com/build/publish-library).

### 1. Add the plugin

In **build.gradle** (Groovy DSL):
```gradle
plugins {
    id 'com.android.library'
    id 'maven-publish'
}
```
Or in **build.gradle.kts** (Kotlin DSL):
```kotlin
plugins {
    id("com.android.library")
    `maven-publish`
}
```

### 2. Configure publishing

In **build.gradle** (Groovy DSL):
```gradle
afterEvaluate {
    publishing {
        publications {
            release(MavenPublication) {
                from components.release
                groupId = 'com.github.YourUsername'
                artifactId = 'YourLibrary'
                version = '1.0'
            }
        }
    }
}
```

Or in **build.gradle.kts** (Kotlin DSL):
```kotlin
afterEvaluate {
    publishing {
        publications {
            register<MavenPublication>("release") {
                from(components["release"])
                groupId = "com.github.YourUsername"
                artifactId = "YourLibrary"
                version = "1.0"
            }
        }
    }
}
```

*(Optional)* With Android Gradle Plugin 7.1+, you can also configure publishing components with sources inside the `android { ... }` block:
```gradle
android {
    publishing {
        singleVariant("release") {
            withSourcesJar()
        }
    }
}
```

## Checks

Check that your library can be installed to mavenLocal (`$HOME/.m2/repository`):

```sh
./gradlew publishToMavenLocal

# or if you named your publication "release":
./gradlew publishReleasePublicationToMavenLocal
```

## Create your release

If everything went well in the previous step, your library is ready to be released! Create a GitHub release or add a git tag and you're done!

## Installing

Users of your library will need to add the jitpack.io repository to their **settings.gradle** file (Groovy DSL):

```gradle
dependencyResolutionManagement {
    repositoriesMode.set(RepositoriesMode.FAIL_ON_PROJECT_REPOS)
    repositories {
        google()
        mavenCentral()
        maven { url 'https://jitpack.io' }
    }
}
```
Or in **settings.gradle.kts** (Kotlin DSL):
```kotlin
dependencyResolutionManagement {
    repositoriesMode.set(RepositoriesMode.FAIL_ON_PROJECT_REPOS)
    repositories {
        google()
        mavenCentral()
        maven { url = uri("https://jitpack.io") }
    }
}
```

And then add the dependency in their module's **build.gradle** (Groovy DSL):

```gradle
dependencies {
    implementation 'com.github.jitpack:android-example:1.0.1'
}
```
Or in **build.gradle.kts** (Kotlin DSL):
```kotlin
dependencies {
    implementation("com.github.jitpack:android-example:1.0.1")
}
```

Note: do not add the jitpack.io repository under `buildscript`. 

## Adding a sample app 

If you add a sample app to the same repo then your app needs to depend on the library. To do this in your `app/build.gradle` (Groovy DSL) add a dependency in the form:

```gradle
dependencies {
    implementation project(':library')
}
```
Or in `app/build.gradle.kts` (Kotlin DSL):
```kotlin
dependencies {
    implementation(project(":library"))
}
```

where 'library' is the name of your library module.

## Examples

- [Library example](https://github.com/jitpack/android-example) 

