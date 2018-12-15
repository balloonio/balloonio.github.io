---
title: Static vs Dynamic Libraries
tags:
  - c
  - c++
  - build
  - compiler
  - linker
date: 2018-12-14 18:50:00
categories:
  - fundamentals
---

This page discusses the fundamental differences and tradeoffs between using static and dynamic libraries for application development, along with specific considerations for why static linking is often chosen for applications at some big companies.

<!-- more -->

# Static Libraries (`.a` files)

`.a` = Archive

Just an archive (like a .tar or .zip) of individual .o files, created by the ar tool.
When linking into an exexutable, the linker extracts and links those objects which contain symbols referenced by the compiled code.

- Historically, all of the object is loaded, so all its dependencies must also be, even if the call chain is never invoked. (With advanced [function-level sections](https://en.wikipedia.org/wiki/Executable_and_Linkable_Format), only the individual functions and variables are added to the executable.)
- Cleaner code that organizes disparate code into different source files is still better than relying on the linker.

# Dynamic Libraries (`.so` files)

`.so` = Shared Object (or a `.dll` file in the Microsoft Windows world)

Contains compiled code from component `.o` files, but those files may no longer exist individually.

When linking into an executable, the linker simply sets up references in the executable to the .so file, which are only realized by the OS when it is loading the executable into memory.

- Objects must be compiled ['PIC'](https://en.wikipedia.org/wiki/Position-independent_code).
- All of the library must be loaded

> AIX objects use XCOFF, and are always PIC, and can also reside in both `.a` and `.so` files.

# Static vs. Dynamic Pros (plus) and Cons (minus)

## Static Libraries - the library exists once in _each_ executable:

- **Plus**: Executable may load faster, because symbol resolution already took place at compile-time.
- **Plus**: Only the objects actually used by the task are included in the executable.
- _Minus_: Consumes additional diskspace and memory for each executable.
- _Minus_: Cannot update executables without relinking.
- **Plus**: Cannot break executables without relinking.

## Dynamic Libraries - the library exists once for _all_ executables:

- **Plus**: If an `.so` is already loaded, the executable may load faster, because it is a smaller file.
- _Minus_: If an `.so` is not already loaded, the executable's remaining undefined symbols must be resolved at start-up.
- _Minus_: All or nothing - the entire library must be loaded if any part of it is required.
- **Plus**: Consumes diskspace and memory only once.
- **Plus**: Can update executables without relinking.
- _Minus_: Possible to break executables without relinking.

# Deployment of Shared Libraries

Since shared libraries put off the final linking of the application until runtime, it is essential that the shared libraries used by an application are compatible with it at a binary level. This is easier to do when libraries have consistently managed [ABI](https://en.wikipedia.org/wiki/Application_binary_interface)s. As a consequence, it is risky to deploy shared libraries used by a number of different applications unless a single team owns the development and release of the stack, applications and libraries together.

# Static Linking of Applications

As a result of the above, applications at big companies are commonly linked statically, except for dependencies on shared libraries that are deployed from DPKG. Even in this case, applications need to be relinked and redeployed consistently to ensure they do not break when the underlying distribution updates.
