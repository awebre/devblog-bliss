---
title: 'Cloudflared as a Service'
date: 2022-11-16
summary: A short tutorial on setting up Cloudflared as a service.
tags:
  - post
  - cloudflared
  - cloudflare
  - networking
---

### A Quick Prologue

Previously, I attempted to get Cloudflare Tunnels running on a Le Potato, so that I could SSH into it from anywhere. Once I had that working, I quickly realized one major flaw in my plan: if Le Potato restarted for any reason, my tunnel would go down and I would be locked out!

Thankfully, the wonderful people at Cloudflare were already one step ahead of me. To fix just this scenario, they have commands built-in to "cloudflared", that will allow you to run your tunnels as a service. This means that any time the machine boots up, it will be available via the tunnel.

This setup is pretty quick, so let's get started.

### Cloudflare Tunnels as a Service

_Note: This tutorial assumes you [already have a cloudflare tunnel configured.](/tunneling-to-le-potato-with-cloudflare/)_

First, install the cloudflare service:

```
cloudflared service install
```

Next, start the service:

```
systemctl start cloudflared
```

Now, to be sure that everything is setup correctly, you'll want to run the following command:

```
systemctl status cloudflared
```

If you don't get an error message (and instead see something like "Status: active"), then you should be good to go. If you're brave, you might even run a reboot and see what happens!

```
reboot
```

In my case, I decided to run the reboot command from a remote instance, while I was far away from home (where Le Potato lives). It was an agonizing few seconds while I waited for the system to boot back up and connect via the tunnel, but it did eventually re-connect! _On the first try!_
