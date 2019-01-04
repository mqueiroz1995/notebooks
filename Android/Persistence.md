:warning: This notebook is heavily based on the documentation found on Google's Android docs: [App data and files](https://developer.android.com/guide/topics/data/)

# Persistence <!-- omit in toc -->

## Table of Contents <!-- omit in toc -->
- [Introduction](#introduction)
- [Comparative](#comparative)
- [Sharing Data](#sharing-data)
- [Securing Data](#securing-data)
- [References](#references)

## Introduction

Android provides several options for you to save your app data. The solution you choose depends on your specific needs, such as how much space your data requires, what kind of data you need to store, and whether the data should be private to your app or accessible to other apps and the user.

This notebooks discusses those options and their advantages and drawbacks.

## Comparative

- **[Internal File Storage](https://developer.android.com/guide/topics/data/data-storage#filesInternal): Store app-private files on the device file system.**
  - Internal storage is best when you want to be sure that neither the user nor other apps can access your files.
  - When the user uninstalls your app, the files saved on the internal storage are removed so you should not use internal storage to save anything the user expects to persist independenly of your app.
  
- **[Internal Cache Files](): Store app-private cache files on the device file system.**
  - If you'd like to keep some data temporarily, rather than store it persistently, you should use the special cache directory to save the data.
  - When the device is low on internal storage space, Android may delete these cache files to recover space. However, you should not rely on the system to clean up these files for you. 
  - You should always maintain the cache files yourself and stay within a reasonable limit of space consumed, such as 1MB.
  
- **[External File Storage](https://developer.android.com/guide/topics/data/data-storage#filesExternal): Store files on the shared external file system. This is usually for shared user files, such as photos.**
  - External storage is the best place for files that don't require access restrictions and for files that you want to share with other apps or allow the user to access with a computer.
  - Requires READ_EXTERNAL_STORAGE permission. Also, the files are still visible to the user and other apps that have the READ_EXTERNAL_STORAGE permission. If you need to completely restrict access, you should instead write your files to the internal storage.
  - The external storage might become unavailable if the user removes the SD card or connects the device to a computer. If your app's functionality depends on these files, you should instead write your files to the internal storage.
  
- **[Shared Preferences](https://developer.android.com/guide/topics/data/data-storage#pref): Store private primitive data in key-value pairs.**
  - Use if you have a relatively small collection of key-values that you'd like to save.
  - When the user uninstalls your app, the values saved on shared preferences are removed so you should not use shared preferences to save anything the user expects to persist independenly of your app.
- **[SQLite/Room](https://developer.android.com/guide/topics/data/data-storage#db): Store structured data in a private database.**
  - When the app handles non-trivial amounts of structured data.
  - When the user uninstalls your app, the values saved on SQLite are removed so you should not use SQLite to save anything the user expects to persist independenly of your app.
  - Room uses SQLite but takes care of a lot of boilerplate code, it is highly recommend using Room instead of raw SQLite.
  
Description/<br>Storage Type | Internal File Storage | Internal Cache | External File Storage | Shared Preferences | SQLite/Room
------------------------ | --------------------- | -------------- | --------------------- | ------------------ | -----------
Data Type                | File                  | File           | File                  | Key-Map            | Relational
Amount of Data           | Large                 | Up to 1MB      | Large                 | Small              | Large
Location                 | Disk                  | Disk           | Disk                  | Disk               | Disk
Visibility               | Private*              | Private*       | Public                | Private*           | Private*
Persisted Until          | App is uninstalled    | App is uninstalled or when system needs space | App is uninstalled or when user deletes file, depending on implementation | App is uninstalled | App is uninstalled

\* Private files can easily be accessed if the user has a rooted device. See [Securing Data](#securing-data) section for more information.

## Sharing Data 
:pushpin: TODO:

## Securing Data

The Android filesystem has a major security drawback: if the device is rooted, the user can access the files without much work. Since all the native persistence alternatives are based in the filesystem, this drawback affects all of them. The conclusion is that there is no safe place to store data on disk and we shall use other techniques to protect sensitive data. We can use encryption to prevent an attacker from being able to understand the data when they gain access to it.

An encryption algorithm will require a key, but where should the key be stored? If we simply store the key in the application, a hacker could de-compile your app and figure out how to decrypt the data. If an attacker can get hold of the decryption key they can read your data, so we have simply transferred the problem of protecting the data, to the problem of protecting the key. You might at this stage ask if we have gained anything?

That's where Android Keystore System play it's role.

### Android Keystore System

:pushpin: TODO: Extract Section to it's own file and go deeper into it's content

- https://developer.android.com/guide/topics/security/cryptography/
- https://source.android.com/security/
- https://proandroiddev.com/android-keystore-what-is-the-difference-between-strongbox-and-hardware-backed-keys-4c276ea78fd0
- https://blog.google/products/pixel/titan-m-makes-pixel-3-our-most-secure-phone-yet/
- https://www.androidauthority.com/project-treble-818225/

Android Keystore system protects key material from unauthorized use. Firstly, Android Keystore mitigates unauthorized use of key material outside of the Android device by preventing extraction of the key material from application processes and from the Android device as a whole. Secondly, Android KeyStore mitigates unauthorized use of key material on the Android device by making apps specify authorized uses of their keys and then enforcing these restrictions outside of the app's processes.

#### Extraction prevention

Key material of Android Keystore keys is protected from extraction using two security measures:
- Key material never enters the application process. When an application performs cryptographic operations using an Android Keystore key, behind the scenes plaintext, ciphertext, and messages to be signed or verified are fed to a system process which carries out the cryptographic operations. If the app's process is compromised, the attacker may be able to use the app's keys but cannot extract their key material (for example, to be used outside of the Android device).
- Key material may be bound to the secure hardware (e.g., Trusted Execution Environment (TEE), Secure Element (SE)) of the Android device. When this feature is enabled for a key, its key material is never exposed outside of secure hardware. If the Android OS is compromised or an attacker can read the device's internal storage, the attacker may be able to use any app's Android Keystore keys on the Android device, but not extract them from the device. This feature is enabled only if the device's secure hardware supports the particular combination of key algorithm, block modes, padding schemes, and digests with which the key is authorized to be used. To check whether the feature is enabled for a key, obtain a KeyInfo for the key and inspect the return value of KeyInfo.isInsideSecurityHardware().

#### Hardware security module

Supported devices running Android 9 (API level 28) or higher installed can have a StrongBox Keymaster, an implementation of the Keymaster HAL that resides in a hardware security module. The module contains the following:
- Its own CPU.
- Secure storage.
- A true random-number generator.
- Additional mechanisms to resist package tampering and unauthorized sideloading of apps.

When checking keys stored in the StrongBox Keymaster, the system corroborates a key's integrity with the Trusted Execution Environment (TEE).

> Impressive as this undoubtedly is, it still may not be completely secure. But then nothing ever is. The point is that even though malware can never extract a key from the secure hardware, it may still be able to use it. Use of keys may be subject to access controls like requiring the user to enter their device password, but if an attacker is able to gain root access then they may be able to install a keylogger (software that records everything the user types - including their password!), and that may then allow the attacker to use any keys stored in the secure hardware. If one of those keys has been used to encrypt data also stored on the device, then the attacker may be able to decrypt the data. Whether such an attack is possible may depend upon the specifics of the device.

:pushpin: TODO: IN WHICH CASES THE KEYSTORE IS LOST?

## References

* https://developer.android.com/guide/topics/data/
* https://developer.android.com/training/sharing/
* https://stackoverflow.com/a/8184699
* https://www.futurelearn.com/courses/secure-android-app-development
