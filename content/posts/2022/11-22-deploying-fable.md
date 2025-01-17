---
title: 'Incognitum: Part 2'
date: 2022-11-22T22:15:00-6:00
summary: 'Deploying a Fable PWA using Azure Static Web Apps.'
tags:
  - post
  - incognitum
  - mastodon
  - devops
  - azure
  - azure-static-web-apps
---

On the last episode of "Building Incognitum", I created a brand new Fable-based React application using Feliz. This app has a very basic form that attempts to solve one small piece of the Mastodon Client problem: initiating an authorization flow. If you read this previous article, you'll know that I promised to keep down that path. However, I lied.

### Previously...

Given the headache I had with getting this Fable-based application up and running, I decided that I'd like to take a break from the Mastodon API and writing more F# and do something a little more relaxing: set up automated deployments. Being relatively well versed in the world of DevOps, I should've known better, but it seemed like a good idea at the time. 🤷‍♂️

### Deploying to Azure Static Web Apps

If you've read the docs around Azure Static Web Apps, they promise a streamlined way to create websites from static content, such as HTML, CSS, and Javascript. I started with the process that it described [in the docs](https://learn.microsoft.com/en-us/azure/static-web-apps/get-started-portal?tabs=vanilla-javascript&pivots=github#create-a-static-web-app), but quickly realized that something was up...

![Figure 1](/images/2022/11/22/figure_1.png)

Now this original error message seemed to suggest that there were issues with the dependencies as they were installed from the template, but everything seemed to work fine for me locally, so I totally ignored that and started frantically Googling. This brought me to a [very helpful dev.to article](https://dev.to/azure/creating-static-web-apps-with-f-and-fable-4nac) with some sample code. I updated my github actions to match this setup, and... still no luck.

At this point, I'm getting desperate, so I re-read the original error message and realize that there are some conflicting npm dependencies that need to be addressed. I updated webpack and slogged through some errors, but that only brought me to a new set of errors, related to Oryx (the tool used for deploying your Static Web App to Azure). Specifically, the error complained about not being able to find the "dotnet" command, which I found odd because there was nothing dotnet related to deploy - Fable compiles down to Javascript, right?

The frantic Googling continues... Eventually, I come across some information that makes it all click: there is apparently a property in "Azure/static-web-apps-deploy@v1" for "skip_api_build". As best I can tell, the "Azure/static-web-apps-deploy@v1" step attempts to detect which type of applications might be present. Inspecting the files present, it determines that there is dotnet code that is present and assumes that the "API" that is meant to back my Static Web App, uses dotnet (when in fact, there is no "API" or Azure Functions here, just a static web app).

The flow of the Oryx deployment ends up being: attempt to build the front end, attempt to build the "API", upload the resulting files to Azure. The problem with this is, I don't have an "API" to build and the front end has actually already been built (thanks to the changes I made following that dev.to article). Once I had informed Oryx that it could skip both of those things, deployment succeeded and we were in business. The result of all this work was [this](https://github.com/awebre/incognitum/tree/5301ff6bb9eea29f5582e3d575f2b4f87dfcbccd) (pay special attention to the [deploy.yml](https://github.com/awebre/incognitum/blob/5301ff6bb9eea29f5582e3d575f2b4f87dfcbccd/.github/workflows/deloy.yml)).

_Feel free to [check the git history](https://github.com/awebre/incognitum/commits/master) between 68e373dae3cb197859bd207dc4471b834832aa48 and 5301ff6bb9eea29f5582e3d575f2b4f87dfcbccd to familiarize yourself with my suffering._

With the deployment complete, I was able to set up a custom domain and the site is now available at
[https://incognitum.thewebres.com](https://incognitum.thewebres.com).

### Making this a PWA

Now, many people would have been happy with getting automated deployments via GitHub and Azure, but there was one other fundamental piece that I felt was missing from this not-yet-useful Mastodon Client: it isn't a Progressive Web App. PWA's are all the rage, and honestly, I'm hoping (somewhat foolishly) to replace the several mobile clients I've downloaded with this app as a PWA. Besides, it can't be that hard, right?

Right! _Well... sort of._

I was able to find a tutorial that walked through most of the steps needed to make any React app a PWA - [on Medium](https://imranhsayed.medium.com/setup-pwa-workbox-webpack-plugin-for-react-application-workbox-window-precaching-caching-at-40f9289650e5). It took a bit of tweaking, but this is what I got to work:

- [manifest.json](https://github.com/awebre/incognitum/blob/f1a17abafddc78565d680c58d64a2f61f14677c3/src/manifest.json), [serviceWorkerRegistration.js](https://github.com/awebre/incognitum/blob/f1a17abafddc78565d680c58d64a2f61f14677c3/src/serviceWorkerRegistration.js), and [src-sw.js](https://github.com/awebre/incognitum/blob/f1a17abafddc78565d680c58d64a2f61f14677c3/src/src-sw.js) all under ./src
- Changes to the [webpack.config.js](https://github.com/awebre/incognitum/blob/f1a17abafddc78565d680c58d64a2f61f14677c3/webpack.config.js) to include the manifest.json in the output directory and to inject the service worker.
- Improvements to the icon assets, including a new icon which was generated using [Stable Diffusion](https://huggingface.co/spaces/stabilityai/stable-diffusion)

_For some helpful image manipulation tricks see [this post](/favicon-generation-with-imagemagick/)._

The end result is a web app that you can install on your device! [Check it out!](https://incognitum.thewebres.com)

![Figure 2](/images/2022/11/22/figure_2.png)
