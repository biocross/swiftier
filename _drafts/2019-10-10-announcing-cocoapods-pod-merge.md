---
layout: post
title: "Announcing the pod merge plugin for Cocoapods!"
subtitle: Improve your app's pre-main time by reducing dynamic frameworks!
author: "Siddharth"
ogimage: "https://github.com/biocross/swiftier/raw/master/assets/posts/pod_merge/logo.png"
---

I'm really excited to announce something I've been working on for quite some time! We recently embarked on a project in Grab to improve the app's startup time: The time it takes from when the user taps on the app icon, to when the first screen is usable (time to interactive). Among other things, we found that one of the major contributors to this time was the pre-main time. 

Pre-main time on iOS is the time the system takes to load your app's binary into memory. including the frameworks used by your app. Apple talked about this in their 2016 WWDC talk [Optimizing App Startup Time](https://developer.apple.com/videos/play/wwdc2016/406), and you can measure this tine in your app by adding the `DYLD_PRINT_STATISTICS` variable in your app's environment variables. 

<img src="https://swiftier.co/assets/posts/pod_merge/env_var.png" alt="DYLD Environment Variable" style="zoom:42%;" />

After adding the variable, you can expect an output like this when you launch your app with the debugger attached:

```
Total pre-main time: 4.7 seconds (100.0%)
         dylib loading time: 4.3 seconds (92.0%)
        rebase/binding time:  26.57 milliseconds (0.5%)
            ObjC setup time: 274.89 milliseconds (5.8%)
           initializer time:  73.07 milliseconds (1.5%)
           slowest intializers :
             libSystem.B.dylib :  22.25 milliseconds (0.4%)
```

For most apps, including ours, the dylib loading time here forms the majority of the pre-main time. This is the time spent by the system loading the dynamic frameworks your app ships with. If this time is higher than 500 ms for you, then there's a good chance you can optimize this number. A common instance when this number is high is when your app uses a lot of cocoapods, and you're using `use_frameworks!` in your Podfile.

To understand what I mean by a 'dynamic' framework, here's a short summary: Your app's main binary can link with frameworks on iOS in two ways:  at compile time (static linking), and at runtime (dynamic linking). If you use static linking, the linker copies over the symbols of the framework into your main binary while compiling your app, thereby removing the startup cost of linking the framework during app startup. Dynamic linking on the other hand compiles the framework, and creates a .framework file that is loaded dynamically during app startup. The time taken by this process is called the dylib loading time.

The first approach you can take to reduce the number of dynamic frameworks you app has to load on startup is use static linking for your frameworks whenever possible. Static linking can be achieved in cocoapods by removing the use_frameworks! directive in your Podfile.

However, there's a few caveats:

* If you remove the use_frameworks! directive from your Podfile, all the pods now get statically linked. This can lead to issues, since some pods cannot be statically linked. There are ways to selectively only some pods statically (coming in a future blog post; this feature is also coming to a future release of Cocoapods)
* If your codebase is divided into multiple internal frameworks, for example `Feature1.framework` &  `Feature2.framework`, and both of these use a pod `A`, this can lead to multiple copies of `A` in your app's final binary, since the pod will be copies to Feature1 once, and Feature2 once, and then the Feature1 & Feature2 frameworks will be copied into to the main binary, creating 2 copies of the external pod. This case is common for UI related pods, which are often used my multiple frameworks within your app.
* Some pods simply do not work when statically linked. This includes pods which contain static libraries inside.

In their WWDC 2016 Talk, Apple suggested that the ideal number of dynamic frameworks your app should ship with is half a dozen: 6. They also mentioned that if your app exceeds this number, you can try and merge them together to improve your app's startup.

However, Apple did not give any more information about how you can actually go about 'merging' your frameworks. If you search on Stack Overflow, and multiple github issues in Cocoapods, people have often asked about how to go about this process, and information is scarce and scattered. Let me try to sum up what I've found:

* Static libraries: You can merge static libraries using a tool called `lipo`, and then link the merged framework with your app. 
* Dynamic frameworks, closed source: Cannot be merged, since the source is not available to you. Common example: Fabric, Crashlytics, Firebase
* Dynamic frameworks, open source: Can be merged, but no automated process exists (*until now!*)

The most interesting category here is open source dynamic frameworks, since this the how most apps use Cocoapods. The source of most pods are visible to your project, and they are compiled along with your app, giving us opportunity to fiddle with how they are linked with your app.

We started by trying to manually merge two randomly picked pods that we used. We copied over their source files and assets into one directory, and created a new local .podspec file with the union of their build settings and configuration. We then pointed our `Podfile` to use the new podspec that we created. Surprisingly, it worked well! When we archived the project, we now saw the merged framework instead of individual pods in the final IPA, and the dylib loading time had a clear decreas when tested on an old device.

The downside of this manual process was, well, it was manual. When we tried to repeat the same excercise with 5 pods, things quickly got out of hand. This was obviously not a scalable way to do things. We repeated the process with more sets of pods, and noted our findings on what worked and what didn't. 

We decided to create a script that could automatically perform this merge process, and apply all the learning we had from our manual merge process. Since cocoapods is one of the major ways people consume third party dependencies, We decided to leverage cocoapods' existing familiarity and created a cocoapods plugin to transparently merge your dependencies every time you run `pod install`!

## Introducting cocoapods-pod-merge

<img src="https://swiftier.co/assets/posts/pod_merge/mergefile.png" alt="MergeFile" style="zoom:42%;" />

<img src="https://swiftier.co/assets/posts/pod_merge/podfile.png" alt="PodFile" style="zoom:42%;" />
