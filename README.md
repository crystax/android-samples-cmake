Simple CMake-enabled example working with CrystaX NDK
============================================================

This is an example showing how to use CMake for building Android binaries
with [CrystaX NDK](https://www.crystax.net/android/ndk) (version 10.4.0 or higher).
Alternatively, you can download [CrystaX NDK daily build](https://dl.crystax.net/builds/)
(build #802 or higher needed).

This sample written in a way to be portable, so it works the same on OS X, GNU/Linux and Android.
Here is example:

Build on OS X and GNU/Linux:
```
  $ mkdir build
  $ cd build
  $ cmake ..
  ....
  $ make
  ....
  $ make run
  [100%] Built target cmake-test
  Scanning dependencies of target run
  Hello, world!
  [100%] Built target run
```

Build for Android:
```
  $ export NDK=/path/to/the/unpacked/crystax-ndk
  $ mkdir build
  $ cd build
  $ cmake -DCMAKE_TOOLCHAIN_FILE=$NDK/cmake/toolchain.cmake -DANDROID_ABI=armeabi-v7a-hard ..
  ....
  $ make
  ....
  $ make run
  [100%] Built target cmake-test
  Scanning dependencies of target run
  Hello, world!
  [100%] Built target run
```
