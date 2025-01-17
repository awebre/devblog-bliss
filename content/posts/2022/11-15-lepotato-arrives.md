---
title: 'La Patate Arrive'
date: 2022-11-15T23:57:00-6:00
summary: 'Today is the day... Le Potato arrives. (Mésaventures de la Potate: Part 1)'
tags:
  - post
  - le-potato
---

### The Prologue

When I first decided to create my own Mastodon instance, I poured through the internet looking for different ways to host it. Being a .NET Developer by trade, my first instinct was to find a way to host this instance in Azure. That seemed perfectly reasonable, considering the Mastodon source code comes ready for docker compose.

As I dove further into what it would take, it became increasingly likely that Azure wasn't going to be a cost effective option. Between trying to figure out exactly what I would need to run in a container (or if I would need multiple containers) and navigating Azure's complicated "Pay-as-you-go" pricing, I finally broke down and started looking for an easier route. Now, I could have gone directly to a Mastodon-as-a-service, I decided I would get setup with a VPS instead.

I'll be honest, I thought that setting up a Mastodon instance on a Virtual Private Server would be reasonably difficult. As it turns out, it can be really easy if you use a self-hosting software management system called Cloudron. All told, it took me about 10 minutes (and that includes entering credit card details) to get my Mastodon instance set up.

But let's face it, that wasn't difficult enough (and it's costing me $10/month). So I went on the internet, bought a knock-off Raspberry Pi (because I don't reward scalpers) and patiently awaited it's arrival.

### Le Potato Arrives

So today is the day. Upon arriving home from work, I gathered up the heap of packages at my doorstep, burst through the door and started shedding the various products of their brown packaging. I quickly popped open the SD Card, popped it into my Macbook Pro and started frantically Googling for how to flash this SD Card with an Ubuntu image that is compatible with Le Potato and get it up and running.

_For more information on that process, check out [Setting Up Le Potato](/setting-up-le-potato/)_

With my shiny new potato configured and ready to go, it was time to start working on setting up a Mastodon server, but there was one thing that was bothering me. My primary driver is a Macbook Pro, so I'm very used to doing this kind of work on the move. Given that Le Potato requires a hardwired internet connection and an external display, I was hopelessly tied to my desk. That seemed like a solvable problem, so I decided to jump down an entirely different rabbit hole: using a Cloudflare Tunnel to securely access Le Potato from anywhere in the world.

_For more information on that process, check out [Tunneling to Le Potato with Cloudflare](/tunneling-to-le-potato-with-cloudflare/)_

Unfortunately for me, navigating Cloudflare Tunnels, Cloudflare Zero Trust, and Cloudflare WARP, ended up being quite a bit more involved than I thought... But that's what this whole project is about - taking a deep dive into unfamiliar waters. After fighting with the cloudflare ecosystem for some time, I was able to successfully access Le Potato via SSH over cloudflare tunnels! Which means, I can now focus on the good parts: **migrating my Mastodon instance to Le Potato.**

But that will have to wait for part duex of Mésaventures de la Potate! 🥔🇫🇷
