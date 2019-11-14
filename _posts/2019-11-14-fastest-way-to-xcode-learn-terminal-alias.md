---
layout: post
title: "The fastest way to Xcode"
subtitle: Using the power of aliases in your Terminal
author: "Siddharth"
ogimage: "https://github.com/biocross/swiftier/raw/master/assets/posts/fastest_xcode_aliases/og.png"
---

<br/><img src="{{site.url}}/assets/posts/fastest_xcode_aliases/og.png" alt="Pod-Merge-Logo" style="zoom:72%;" /><br/>

As a developer, you probably spend a lot of time in the Terminal, whether you’re rocking git commands, or editing text files in vim. While most of the commands in terminal are already pretty short, like **ls**, or **cd**, some frequently used ones can be longer... for example, to install dependencies with cocoapods in your Xcode project with a repo update, you’d have to run: `bundle exec pod install —-repo-update` 

One way you can get around faster in your Terminal is setting up an **alias**. Think of an alias as you own custom command, which can expand into an existing command! And the best part, it’s super easy to create!

I use a lot of aliases in my Terminal to get around faster: 

* `o` to open the current folder in Finder (open .)
* `c` for clearing the terminal (clear)
* `pi`  to run pod install in the current directory. 

Aliases are a super handy way to make your most typed commands easier to run.

# Creating your first alias
Let’s start with a simple alias, that opens the current folder in Finder.

To do this normally, you can type

```bash
$ open .
``` 

Let’s create an alias `o` , as a shorthand for the above command. Just type:

```bash
$ export alias o='open .'
```

Aaaaand it’s done! You define an alias using the keyword **alias**, followed by the actual alias (this is what you’ll type to run this alias, here *o*). You then specify what command actually runs, after the *=*, wrapped in quotes. Make sure you do not add a *space* anywhere around the **=**, bash doesn’t like that!

With that, now if you just type `o` in your terminal, it should work! Congratulations, your first alias is ready! 🏆

# Get To Xcode Faster ⚡️
As an iOS Developer, I often want to quickly open the Xcode project in the current directory. 

As you can guess, we can create an alias for doing exactly this! There’s an important thing to keep in mind though: I want to open the **.xcworkspace** file if the project is using Cocoapods. If not, the **.xcodeproj** is the way to go.

Let’s create our own alias that's smart enough to undersrand this.

The first thing we need to do, is find the name of the **xcworkspace** or **xcodeproj** file in the current directory. The **find** command is our friend:

```bash
$ find . -name *.xcworkspace || find . -name *.xcodeproj 
```

The **\|\|** operator above makes sure we try to find **.xcodeproj** files only if we fail to find **.xcworkspace** files with the first command. It's a powerful operator to run commands only if previous ones fail.

Now to open a file, we can continue to use the **open** command, passing in the name of the file we want to open.

```bash
$ open $(find . -name *.xcworkspace || find . -name *.xcodeproj) 
```

You’ll notice the `$()` above. That’s a simple way to pass the output of one command as an input to another. Here, we pass the output of **find**, which is the filename of the project in the folder, to the **open** command.

And that’s it! Let’s convert this into an alias called **xc**

```bash
$ export alias xc='open $(find . -name *.xcworkspace || find . -name *.xcodeproj)' 
``` 
  
After you run that, just type **xc** into any folder containing an Xcode project and boom💥: Xcode should fire right up (and start indexing your project, forever 🤣) 

# Saving your Aliases
One of the things you’ll notice if you restart your Terminal is that the aliases you created are gone 😲. This is because we only exported the aliases into the current Terminal session (that’s why we used the **export** keyword)

To make sure your aliases are always available, you need to define them in a file called **.bash_rc**.

This is easy, just open your **.bash_rc** (or **.zshrc** if you’re using **oh-my-zsh**) file with an editor like **nano**:

```bash
$ nano ~/.bash_rc
```

Go to the end of this file (doesn't matter if it's empty), add a new line, and define all the aliases you want:

```bash
alias o='open .'
alias c='clear'
alias pi='bundle exec pod install || pod install'
alias xc='open $(find . -name *.xcworkspace || find . -name *.xcodeproj)'
```

Notice you don’t need the **export** keyword here. Now just save and exit (Press `Ctrl + X`, and then `Y` if you used **nano**), and you're done! Restart your Terminal one last time, and your mighty aliases are ready for use ✅