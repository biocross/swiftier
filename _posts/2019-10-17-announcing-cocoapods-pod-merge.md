---
layout: post
title: "Announcing the pod merge plugin for Cocoapods!"
subtitle: Improve your app's startup time by reducing dynamic frameworks
author: "Siddharth"
ogimage: "https://github.com/biocross/swiftier/raw/master/assets/posts/pod_merge/logo.png"
---

If your app uses a lot of external dependencies in the form of cocoapods, and you’d like improve your app startup performance, I have good news for you!

The pre-main time of apps (especially the dylib load time) can get especially high if you app uses a lot of cocoapods, and you use the `use_frameworks` directive in your Podfile, linking your pods dynamically. These dynamically linked pods are then loaded one by one on app’s startup, slowing it down considerably.

If you’ve watched Apple’s 2016 WWDC talk [Optimizing App Startup Time](https://developer.apple.com/videos/play/wwdc2016/406), you might have noticed this slide, where Apple recommends you to merge your dynamic frameworks.

<img src="{{site.url}}/assets/posts/pod_merge/apple_dylib_merged.png" alt="MergeFile" style="zoom:50%;" />

I’m delighted to announce a cocoapods plugin to do just that! Introducing [_cocoapods-pod-merge_](https://github.com/grab/cocoapods-pod-merge). Here's what it does:

### Merges your pods based on a MergeFile
The plugin introduces a new file to your workflow, the MergeFile:

<img src="{{site.url}}/assets/posts/pod_merge/mergefile.png" alt="MergeFile" style="zoom:72%;" />

You define groups of pods you want to merge, and add pods to group just like you would to a Podfile. Pods inside the group are merged automatically when you run `pod install`!

### Can merge both Swift & Objective-C Pods

The plugin can merge both Objective-C and Swift Pods. Mixing them in the same group however is not recommended, due to special settings required for Swift pods.

### Can merge pods that are dependant on each other, even fixing imports!

Add the `has_dependencies!` flag to a group, and the plugin takes care of merging pods that are dependant on each other. 

<img src="{{site.url}}/assets/posts/pod_merge/has_dependencies.png" alt="PodFile" style="zoom:62%;" />

It even fixes imports inside the merged pods, since the pods are now part of the same framework! 

### Minimal changes to your Podfile

<img src="{{site.url}}/assets/posts/pod_merge/podfile.png" alt="PodFile" style="zoom:62%;" />

You only need to add one line to your Podfile to start using the plugin, and use the merged pods instead of the individual ones!

### Keeps logical separation between merged pods using C modulemaps

After merging the pods, the plugin automatically creates a C modulemap for the resulting framework, helping you still import the merged pods individually! 

<img src="{{site.url}}/assets/posts/pod_merge/modulemap.png" alt="PodFile" style="zoom:52%;" />

Yay for no import pollution! (except Swift pods)

### Share code between you app and extensions, without duplicate symbols

Share these merged dynamic frameworks between your app's main binary and extensions, without creating multiple copies of the merged cocoapods within them unlike static linking.

### It's open-source!

[Cocoapods-pod-merge is open source](https://github.com/grab/cocoapods-pod-merge), and we’re excited to get your feedback on the plugin and supporting more use cases in the future!

Checkout the step by step [getting started guide](https://github.com/grab/cocoapods-pod-merge#installation) in project readme, the [performance improvements](https://github.com/grab/cocoapods-pod-merge/blob/master/wiki/benchmarks.md) you can expect, and important [tips and tricks](https://github.com/grab/cocoapods-pod-merge#tips) on how to start grouping and merging pods used by your app. You can even find out [how the plugin works](https://github.com/grab/cocoapods-pod-merge/blob/master/wiki/inner_workings.md) if your're curious!

There's an [example project](https://github.com/grab/cocoapods-pod-merge/tree/master/PodMergeExample)  as well to see the plugin in action! 

The plugin is designed to be integrated right into your existing pod install workflow, so that everything works as it is.

Here’s to faster apps! 🍻
