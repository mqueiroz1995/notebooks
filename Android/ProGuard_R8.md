# ProGuard/R8 <!-- omit in toc -->

## Table of Contents <!-- omit in toc -->
- [Introduction](#introduction)
- [Setup](#setup)
- [Rules](#rules)
- [De-obfuscating Code](#de-obfuscating-code)
- [References](#references)

## Introduction

ProGuard and R8 are mainly optimization tools, that do their job by applying the following operations to the code used by our application (including libraries):

- **Shrinking**: detects and removes unused code
- **Optimizing**: analyzes and optimizes the byte code.
- **Obfuscating**: renames the optimized classes and methods with short names

After applying those operations to our codebase we will have a smaller apk and an obfuscated code that is harder to reverse engineer.

## Setup

Setup is simple:

- To enable ProGuard you just need to add `minifyEnabled true` to the `build.gradle` file of the module. 
- After shrinking the code, you can also remove all the unused resources in the application by adding `shrinkResources true`.

Normally we want to enable ProGuard only for our release
builds, as it’s an additional step that makes the build slower and can make debugging more difficult.

```groovy
buildTypes {
    release {
        minifyEnabled true
        shrinkResources true
        proguardFiles getDefaultProguardFile('proguard-android.txt'),
                    'proguard-rules.pro'
    }
```

When you use Android Studio 3.4 or Android Gradle plugin 3.4.0 and higher, R8 is the default compiler that converts your project’s Java bytecode into the DEX format that runs on the Android platform.

## Rules

Code shrinking is the process of removing code that ProGuard determines is not required at runtime.

To shrink your app’s code, R8 first determines all entry points into your app’s code. Starting from each entry point, the tool inspects your app’s code to build a graph of all methods, member variables, and other classes that your app might access at runtime. Code that is not connected to that graph is considered unreachable and may be removed from the app.

However we can have problems with annotation processing and code generation, reflection, java resource loading and native code (JNI). 

For that, we have rules files that modify its default behavior and better understand your app’s structure, such as the classes that serve as entry points into your app’s code.

## De-obfuscating Code

So know we have a smaller application that makes reverse engineering harder. The problem is that this will also change our crash logs, making them unreadable. How can we prevent that? 

When ProGuard finishes running, it produces 4 output files. They are:

- `usage.txt` – Lists code that ProGuard removed.
- `dump.txt` – Describes the structure of the class files in your APK.
- `seeds.txt` – Lists the classes and members that were not obfuscated. Good to verify that you have obfuscated your important files.
- `mapping.txt` – The file that maps the obfuscated names back to the original.

These files are saved at `<module-name>/build/outputs/mapping/<build-type>/`, and the last one,`mapping.txt`, is the one who is going to help us with that. It is just simple text file which contains original field, method, and class names mapped to the obfuscated names.

It is important to note that this file is recreated after every build.

## References

* [Shrink, obfuscate, and optimize your app - Android Developers](https://developer.android.com/studio/build/shrink-code)
* [ProGuard Docs](https://www.guardsquare.com/en/products/proguard/manual/usage#keepoptions)
* [Little more about ProGuard for Android - Karthikraj Duraisamy](https://android.jlelse.eu/little-more-about-proguard-for-android-5aed2e18f6f1)
* [Troubleshooting ProGuard issues on Android - Wojtek Kaliciński](https://medium.com/androiddevelopers/troubleshooting-proguard-issues-on-android-bce9de4f8a74)
* [Practical ProGuard rules examples - Wojtek Kaliciński](https://medium.com/androiddevelopers/practical-proguard-rules-examples-5640a3907dc9)
* [R8, the new code shrinker from Google](https://android-developers.googleblog.com/2018/11/r8-new-code-shrinker-from-google-is.html)
* [ProGuard and R8: a comparison of optimizers - GuardSquare](https://www.guardsquare.com/en/blog/proguard-and-r8)
