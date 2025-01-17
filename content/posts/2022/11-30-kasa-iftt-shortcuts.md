---
title: 'Automating Kasa Smart Plugs with Siri Shortcuts'
date: 2022-11-30
summary: "Christmas time is here, which means it's time for high-tech light timers."
tags:
  - post
---

According to my wife, it is officially Christmas. Now, I'm not exactly one to get fully into the "Christmas spirit", but I do contribute to the holiday cheer with a little bit of home automation. This all started one fateful christmas season, when I got tired of unplugging the Christmas tree before going to bed. I could have bought a physical timer, like a normal person, but I had been listening to that year's "Shopping Spree" episode of [Coding Blocks](https://www.codingblocks.net/) and one of the hosts mentioned the Kasa smart plugs from TP-Link (his list can be found [here](https://www.codingblocks.net/podcast/the-2021-shopping-spree/#more-20074)).

### Christmas, Christmas Time is Here

I did a little bit of research and found out that the Kasa line of smart products had it's own app, but that this app integrated with Siri Shortcuts. That seemed like an ideal way for me to get into this whole "home automation" crazy. I had previously used Siri Shortcuts to send a text to my wife when I got in my car to go home, so I was familiar enough to feel like this would be a good fit.

And it largely was. That year, I set up a number of scenes in the Kasa app that were then accessible to be triggered via Shortcuts. Here was my set up:

- When Wind Down Starts (at 9:30pm), trigger "lights out"
- When I get out of the car (Carplay Disconnects) and am home, trigger "lights on"
- When I get into the car (Carplay Connects) and am home, trigger "lights out"

When the holiday season was over, I tucked away the smart plugs and didn't think twice about them. (Although I did end up buying some more of the outdoor variants to use for my power tool batteries).

### Home Automation Meets Bit Rot

This year, when the Christmas tree came out again, I quickly reached for my trusty Kasa smart plug. I plugged it into the outlet and watched the lights blink orange and then blue as it remembered my home network and reconnected. Everything seemed to be going according to plan, except that I immediately noticed that my Shortcuts were no longer working. In fact, the Kasa shortcuts were all displaying a notification stating: "This action won't be supported in future versions of Kasa".

I did some quick Googling and found a variety of issues between Kasa and Shortcuts, but most people seemed to be able to resolve them through various combinations of removing/re-adding devices and scenes. None of these seemed to work for me. In fact, they made the problem worse as each time I removed a device or a scene, it was lost forever. Shortcuts did not seem to be aware of any of the new devices or scenes that were being added to Kasa. At this point, I turned to the official Kasa documentation and found another startling piece of information: their official documentation now recommends using IFTT to integrate with Shortcuts.

This was quite annoying to me. I'm familiar with IFTT (in fact, I probably have an ancient account floating around somewhere in the ether), but the whole point of this was that it was supposed to be simple, _right?_ I suppose I was wrong in that assumption, but after fiddling with the IFTT integration for a bit, I got it working. So let's take a look at the easiest way to connect Kasa to Shortcuts (assuming you can't get their built in Shortcuts to work).

### Kasa > IFTT > Siri Shortcuts

The first thing I did was get my scenes together in the Kasa app. In my case, I have a "Night" scene, which turns off the Christmas Tree as well as my power tool batteries (just in case I left them on).

![A screenshot of a Kasa scene with an action to turn Christmas Tree off and an action to turn off the 'Batteries' group.](/images/2022/11/30/fig_1.PNG)

Next, I logged into the IFTT app (created an account as needed) and searched for "Kasa". I found an option like the one shown below:

![A screenshot of the IFTT app, with the search 'Kasa' entered and the TP-Link Kasa icon showing.](/images/2022/11/30/fig_2.jpeg)

I clicked "Connect" which asked me to log in to my TP-Link Kasa account and authorized the connection between IFTT and Kasa. Once this was complete, I clicked "Create", which brought me into the IFTT Applet workflow. Step one is to add the "Button Widget" service and select "Button press" as the action, ending up with something like this:

![A screenshot of the IFTT create Applet Workflow, with the 'IF' section filled with the button press widget.](/images/2022/11/30/fig_3.PNG)

_Note: you could also do this via the IFTT webhooks, which the official Kasa guides recommend, but using the button widget is much easier (trust me)_

Step 2 was to click "Add" next to "Then That" and search for "Kasa" again. Here I had several options to choose from, but I chose to go with "Activate scene," which brought me to a screen where I could select from the various scenes available. With my scene selected, I clicked "Create Action".

_Note: these scenes only seem to sync once, so when I eventually added additional scenes I had to "Reconnect" my Kasa account by going back to "Explore" in IFTT, searching "Kasa" again, and finding the "Reconnect" option (hidden behind a gear icon)._

The results looked something like this (this is the Edit version of the same workflow)

![A screenshot of the IFTT app, with the search 'Kasa' entered and the TP-Link Kasa icon showing.](/images/2022/11/30/fig_4.jpg)

From here, I clicked "Continue", gave my Applet a title and I was done with IFTT!

With IFTT out of the way, it was time to create my shortcuts. I decided to go with "Home Automation" shortcuts, as those meant that any one could trigger them with their voice using our HomePod mini. The actual Shortcut is pretty straight forward, it is a single IFTT "Trigger Applet" Shortcut that triggers the "activate Night" IFTT widget button.

![A screenshot of the Shortcut app with a single IFTT 'Trigger Applet' Shortcut with the 'activate Night' applet selected.](/images/2022/11/30/fig_5.PNG)

Once I had this set up, I was able to replace my existing Kasa Shortcuts in some of the Automations that I had previously had. For example, I was able to reuse the existing Shortcut that I had set up to run when Carplay Connects:

![A screenshot of a Shortcut that compares the phone's current address to a static address and runs the 'Winding Down' shortcut if they match.](/images/2022/11/30/fig_6.jpg)

And that's how I got my Kasa smart plugs integrated again with all my favorite Shortcuts!
