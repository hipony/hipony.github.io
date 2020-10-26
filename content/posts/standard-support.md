+++
baseURL = "https://hipony.github.io/blog"
title = "Falsehoods C++ Programmers Believe About Standard Version Support"
date = 2020-10-24T16:19:54+03:00
tags = ["cpp"]
categories = []
draft = false
+++

```cpp
int oh_no() { return __cpluscplus; } // Check GCC 4.6 and 4.7
```

While being occasionally involved in the C++ package management process [with Conan](https://github.com/conan-io/conan) I see beginners and even experts alike being very confused about what does it mean to support a certain standard version in multi-platform libraries.

Here I'll point out some common misbelieves people have about standard version support.

<!--more-->

## It's enough to know which standard a library supports

Very often folks advertise their libraries as "A Very Cool C++17 Library!". It's okay to get a general idea of which features we _expect_ it to have, but it doesn't help us when trying to consume it on arbitrary platforms.

Take for example the [ranges-v3 library](https://github.com/ericniebler/range-v3). In the description it says that it supports C++14/17/20, but in the minimal compiler version requirements it says:

> Visual Studio 2019 (or later) on Windows, with some caveats due to range-v3's strict conformance requirements: range-v3 needs /permissive- and either /std:c++latest or /std:c++17

**We need information about the minimal compiler version support**.

## Compilers support a certain standard

I often see beginners asking when a certain compiler will support C++XX when they hear that the next ISO standard is published. For people familiar with the C++ ecosystem this question doesn't quit make sense. Compilers start to implement features even before the standard is finalized and may keep doing so for multiple versions forward, gradually releasing separate bits as they're ready.

Quite often some features are behind additional compiler flags such as the `/permissve-` flag in MSVC (no alternative tokens without it!) or the `-lstdc++fs` in GCC (to be able to use `std::filesystem` pre GCC 10).

Or some features may be in the `experimental` namespace, which require quite non-trivial feature detection logic to depend on. In some cases, even if the header is present, [compilers will not allow to use such headers unless you pass correct flags](https://godbolt.org/z/eh76M6).

**We need information about additional actions required to enable certain features. The standard flag is the most basic requirement for that**.

## Library supports a certain standard

As we understand at this point - to determine which standard version a library supports isn't a trivial matter. More so to keep that support during development.

It's a very common situation where certain compiler versions support future standards only partially. Take for example GCC 6.4 - it provides `experimental/filesystem` and `experimental/optional`, but not `experimental/variant`. An author may decide to support this platform if the only thing they really need is a cross-platform filesystem implementation without external dependencies.

The only compiler version that we can be sure of is the one used during development.

**We must test library against declared minimal compiler versions for each release to keep the support information up to date**.

## Library knows which compiler version it needs

Big industry standard libraries such as [Boost](https://github.com/boostorg/asio/blob/develop/include/boost/asio/detail/config.hpp#L95) and [Qt](https://code.woboq.org/qt5/qtbase/src/corelib/global/qcompilerdetection.h.html) do a tremendous amount of work to correctly implement reliable cross-platform feature detection and conform to it.

The issue is that it's not enough to simply copy paste feature detection logic from big libraries, as a library author one must know where to apply the polyfil macro and it's very tricky to do especially for smaller features.

Sometimes authors do a shortcut (I don't have a Mac, how can I know if my library supports it?) and simply check [public lists of supported features](https://en.cppreference.com/w/cpp/compiler_support). But such lists tend to get outdated or contain not a full information, especially with respect to the library support.

Until we build our library on a certain compiler version with a certain standard - we can't be sure that our library supports that compiler version.

**We should not reject unknown compiler versions. It's better to try to build and fail**.

## Compilers never lie

Well, not quite. They have _bugs_ instead. Or sometimes [they're not required to provide diagnostic](https://godbolt.org/z/6PaK5T).

A common misconception is that we can ask a compiler nicely which feature it supports and everything will be _fine_. But what if a certain version of a compiler doesn't provide a list of known features? What if the information it provides isn't _granular_ enough?

> Did you know that CMake discourages the use of _granular_ `target_compile_features` in favor of 'meta' features such as `cxx_std_17`?

**The only reliable way to detect standard feature support is to try to compile the code itself and run its tests**.

## Conclusion

* We need information about the minimal compiler version support
* We need information about additional actions required to enable certain features. The standard flag is the most basic requirement for that
* We must test library against declared minimal compiler versions for each release to keep the support information up to date
* We should not reject unknown compiler versions. It's better to try to build and fail
* The only reliable way to detect standard feature support is to try to compile the code itself and run its tests
