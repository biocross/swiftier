---
layout: post
title: "Announcing the pod merge plugin for Cocoapods!"
subtitle: Improve your app's pre-main time by reducing dynamic frameworks!
author: "Siddharth"
ogimage: "https://github.com/biocross/swiftier/raw/master/assets/posts/pod_merge/logo.png"
---

I'm really excited to announce something I've been working on for quite some time! We recently embarked on a project in Grab to improve the app's startup time: The time it takes from when the user taps on the app icon, to when the first screen is usable (time to interactive). Among other things, we found that one of the major contributors to this time was the pre-main time. 

Pre-main time on iOS is the time the system takes to load your app's binary into memory. including the dynamic frameworks used by your app. Apple talked about this in their 2016 WWDC talk [Optimizing App Startup Time](https://developer.apple.com/videos/play/wwdc2016/406), and you can measure this tine in your app by adding the `DYLD_PRINT_STATISTICS` variable in your app's environment variables. 

<img src="{{site.url}}/assets/posts/pod_merge/env_var.png" alt="DYLD Environment Variable" style="zoom:42%;" />

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



