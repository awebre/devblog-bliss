---
title: 'A Clean Start: Dev Environment Setup with Winget'
date: 2024-03-08
description: 'Using Winget (Windows Package Manager) to automate dev box app installations.'
tags:
  - post
  - winget
  - dev-machine
---

Earlier this week, things started getting really strange on my work PC. Rider (THE best IDE for .NET, sorry Microsoft) stopped being able to open certain projects, Docker containers that I've been running for months stopped working, and things generally just felt _off_. This particular PC was built back in 2019 and has been accruing cruft ever since. A recent inspection of disk space showed that I had nearly filled the 900 GB SSD, but with now one obvious culprit. It was just time.

### Time to Reformat

So I backed up relevant files and started reformatting the machine.

A few hours later, I was staring at something I haven't seen in at least 2 years - a completely fresh OS install. My excitement was immediately replaced
with dread. How am I going to get myself back into state of any form of productivity? Enter
[winget](https://github.com/microsoft/winget-cli), the official Windows Package Manager.

After scouring the internet for a few minutes, I came across various articles and GitHub Gists where people have put together their own personal lists of apps that
they like to have installed on their machines. So I copied the shell of those scripts to
[my own GitHub Gist](https://gist.github.com/awebre/ddb09cfe410e54985b38a5ca29f0612c)
and began working on building my list of apps. The process looked something like this:

Start by searching `winget` for an app that you need. For example, I started with a search for Visual Studio Code with
something like

```
winget search "Visual Studio Code"
```

This will return a powershell table of items, with an ID that will look something like "Microsoft.VisualStudioCode". Add a new entry to the list of apps in the powershell
script with the "Name" set to that ID.

Once you've built up your list, run your powershell script and watch in wonder as all of your favorite apps are installed! Forgot something? Just search, add it in, and run it again!
