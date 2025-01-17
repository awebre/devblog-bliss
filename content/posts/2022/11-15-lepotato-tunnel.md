---
title: 'Tunneling to Le Potato with Cloudflare'
date: 2022-11-15T22:45:00-6:00
summary: Exposing a potato over the internet, with care.
tags:
  - post
  - le-potato
  - cloudflare
  - networking
---

### I Promise This Is Not an Ad

Being a developer, I know that exposing devices over the internet is always a risky business.
Not being well versed in server administration, I have no clue how to securely expose a server
on my home network to the internet at large. That's why I turned to _Cloudflare_,
a trusted name in the realm of all things internet.

In truth, I had recently heard about an offering of theirs that allowed you
to create secure tunnels to a device, without directly exposing ports to the internet
at large. Given that this is exactly what I wanted (a secure, direct connection to my home
server without making it available to the broader internet), this seemed like the perfect
time to try it out.

### Getting Cloudflared

_Note: this guide assumes you have a Cloudflare account and at least one domain configured with Cloudflare._

As it turns out, the process for starting up a Cloudflare Tunnel begins with
installing a CLI tool, called Cloudflared. The tool is available for a wide range
of operating systems and architectures, but for Le Potato, I downloaded the
"cloudflared-linux-arm64.deb" package:

```
wget -q https://github.com/cloudflare/cloudflared/releases/latest/download/cloudflared-linux-arm64.deb && dpkg -i cloudflared-linux-arm64.deb
```

With the Cloudflared CLI installed, it's time to login:

```
cloudflared tunnel login
```

The result of this command is some text letting you know that a browser window
should have opened, prompting you to be logged in. But we're in terminal-only land,
so that ain't gonna work. This prompt should also include the URL that the browser
would have opened to (if it could have). You will need to copy this URL into a browser
window on any other device, and log in to your Cloudflare account.

Once you've successfully logged in and selected a domain, the Cloudflared command
on Le Potato should complete, leaving behind a new directory ".cloudflared". In this
directory are some keys that it will need later.

Now it's time to create our tunnel:

```
cloudflared tunnel create home
```

_Note: "home" is the name I decided to give my tunnel, you can call it anything you want._

The result should be a file
that will look like "home/ubuntu/.cloudflared/~GUID~.json". Make note of this GUID - you'll need it later.
(Feel free to copy it to the clipboard, if you can find out how...)

Just to be sure that it worked, you can run:

```
cloudflared tunnel list
```

If all is well, you should see your tunnel's name in the list, along with some other information.

Lastly, you'll need to add a config file at "home/ubuntu/.cloudflared/config.yml" that looks like this:

```
tunnel: ~your-tunnel-guid-from-earlier~
credentials-file: home/ubuntu/.cloudflared/~your-tunnel-guid-from-earlier~.json

ingress:
    - hostname: subdomain.yourdomain.net
      service: ssh://localhost:22
    - service: http_status:404
```

This config exposes ssh over the Cloudflare tunnel and returns 404 for any requests to the tunnel
that do not match that SSH port (22).

### Cloudflare for Teams

The remaining part of this setup requires you to sign up for "Cloudflare for Teams". This part
is a bit of a mess, so I would recommend stepping through Step #3 of
[this Cloudflare blog](https://blog.cloudflare.com/ssh-raspberry-pi-400-cloudflare-tunnel-auditable-terminal/),
which covers it pretty well. The basic idea is that you'll sign up, then create an application, which
will allow you to define rules around who can access the tunnel.

Once you've set up your application, it's time to hop back over to Le Potato and finish up the setup.

First you'll run:

```
cloudflared tunnel route dns ~your-guid~ subdomain.yourdomain.net
```

_This adds a CNAME record in Cloudflare that will route back to the tunnel._

Then you should be ready to run your tunnel:

```
cloudflared tunnel run your-tunnel-name-here
```

If all has gone according to plan, you should now be able to visit subdomain.yourdomain.net and see the Cloudflare Teams
authentication flow. Once you get past that, you should be able to enter your username and password to
gain access to an SSH session on the server!

_Want your tunnel to persist across reboots? [Run Cloudflared as a Service](/cloudflared-as-a-service/)_
