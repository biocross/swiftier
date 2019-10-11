---
layout: post
title: "Xcode vs xcodebuild"
subtitle: Does the command line compile faster?
author: "Siddharth"
---

I recently came across a few discussions online about how Android developers were bypassing Android Studio and using gradle in the command line to compile their projects faster. I asked around my android counterparts in my team, and indeed they were using the same trick, getting improvements of up to ~10-15% per incremental build. This made me wonder if Xcode's UI also had such an overhead, and using Xcode's command line tool `xcodebuild` directly could improve our incremental build times? After all, even a 10% decrease in build times would be significant on huge codebases: the time quickly adds up as your build and run repeatedly.

## Benchmarks

I decided to run the benchmarks using Xcode 10.3, on my 2018 MacBook Pro:

<img src="https://swiftier.co/assets/posts/xcode_vs_xcodebuild/macbook_specs.png" alt="Macbook Specs" style="zoom:40%;" />

I compiled a fairly large codebase, with a majority of Swift (98%), and a little bit of Objective-C (2%). A total of around `xxxx` thousand lines of code / source files (excluding cocoapods).

I cleaned up Xcode's cache `DerivedData`, to make sure it's a fresh clean build, and only compiled for the `i386` architecture, targeting the simulator.

To measure the time taken, I just used the stopwatch on my phone, nothing fancy. 

All in all, using Xcode, it took **533** seconds, that's 8 minutes 53 seconds.

Now was the time to compare this to the command line build. I used the command:

```bash
$ xcodebuild -workspace 'X.xcworkspace' -scheme 'XXX' -arch i386 -sdk iphonesimulator12.4
```

I started the stopwatch with high hopes, considering the gains from our Android counterparts.

Unfortunately, the build finished just shy of 9 minutes - **539** seconds to be exact. 

Weirdly, It took pretty much the same time, if not slightly higher than Xcode itself. Looks like Xcode, unlike Android Studio, does not have an overhead during compilation. In fact, it might even slightly optimise the build process, considering our result. Anyway, since I was benchmarking compilation time, I started exploring tips and tricks other developers have published over the years to improve Xcode compile times. Let's benchmark some of these!

### Disabling dSYM Generation

### Turning off Whole Module Optimization 

### Improving Linker Configuration

### Using Cocoapods-binary to Precompile Pods