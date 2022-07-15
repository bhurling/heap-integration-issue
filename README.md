# Heap SDK integration
This repository highlights an issue when integrating the Heap Android SDK

The basic installation instructions for the [Heap SDK](https://developers.heap.io/docs/android) do not describe how to set up the Plugin with the Plugin DSL.

## The problem

If you're running into an issue such as:

```
A problem occurred evaluating project ':app'.
> Failed to apply plugin 'com.heapanalytics.android'.
   > Could not create plugin of type 'HeapPlugin'.
      > Could not generate a decorated class for type HeapPlugin.
         > com/android/build/api/transform/Transform
```

you might want to try the following setup instead:

## The solution

Add this resolution strategy to your `settings.xml`

```kotlin
// settings.gradle.kts

pluginManagement {
    repositories {
        gradlePluginPortal()
        google()
        mavenCentral()
    }
    // Add a resolution strategy to resolve the right module for "com.heapanalytics.android"
    resolutionStrategy {
        eachPlugin {
            if (requested.id.id == "com.heapanalytics.android") {
                useModule("com.heapanalytics.android:heap-android-gradle:${requested.version}")
            }
        }
    }
}
```
After that you'll be to use apply the plugin with its id:

```kotlin
// build.gradle.kts in app module

plugins {
    id("com.android.application") version "7.2.1"
    id("com.heapanalytics.android") version "1.10.4"
}
```

This also works with version catalogs:

```toml
# libs.versions.toml

[versions]
agp = "7.2.1"
heap = "1.10.4"

[plugins]
android_application = { id = "com.android.application", version.ref = "agp" }
heap = { id = "com.heapanalytics.android", version.ref = "heap" }
```

```kotlin
// build.gradle.kts in app module

plugins {
    alias(libs.plugins.android.application)
    alias(libs.plugins.heap)
}
```
