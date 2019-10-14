---
layout: post
title: "Must have quicklook plugins for Developers"
subtitle: Supercharge Finder on your Mac!
author: "Siddharth"
---

I absolutely love the quicklook feature on macOS, which let's you smash the space button on any file in Finder and see a quick preview without opening it. This is amazing for videos, text files, and even quickly skimming through documents. However, QuickLook doesn't support all file formats by default.

For example, as a developer, you might work with `.md` Markdown files, or `.json` files frequently, which quicklook doesn't support by default. Worry not, there are some awesome QuickLook plugins that's have been developed by the community, and will quickly become an essential part of your workflow!

> A quick note on how to install these plugins: Most of the download links will give you a `.qlgenerator` file. To install these plugins, copy this file to `~/Library/QuickLook/` directory on your mac. After that, just restart the QuickLook manager by opening Terminal and running `qlmanage -r`. That's it, all done! 

## qlmarkdown: Markdown QuickLook plugin:

This awesome plugin, as the name suggests, lets you preview markdown files in quicklook!  Really handy if you want to look a project README or documentation.

<img src="{{site.url}}/assets/posts/quicklook/markdown.png" alt="QLMarkdown" style="zoom:50%;" />

You can download it [here](https://github.com/toland/qlmarkdown).

## quick look JSON

As the name suggests, this lets you preview JSON files in quicklook!

<img src="{{site.url}}/assets/posts/quicklook/json.png" alt="quickLookJSON" style="zoom:50%;" />

You can download it [here](http://www.sagtau.com/quicklookjson.html).

## QLColorCode: Preview code files

This awesome plugin lets your preview code files with syntax highlighting! Super handy!

<img src="{{site.url}}/assets/posts/quicklook/syntax.png" alt="QLColorCode" style="zoom:50%;" />

You can download it [here](https://github.com/anthonygelibert/QLColorCode).

These are the quicklook plugins that make my life easier on a daily basis! Interestingly, while writing this post, I discovered this awesome repository which has more amazing plugins: [quick-look-plugins](https://github.com/sindresorhus/quick-look-plugins). The one that can preview zip files is a godsend! Happy coding!