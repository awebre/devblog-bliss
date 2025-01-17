---
title: 'Incognitum: Part 1'
date: 2022-11-21
summary: "I've decided to take on an additional side project: a Mastodon client written in Fable."
tags:
  - post
  - incognitum
  - mastodon
  - fsharp
  - fable
---

Due to some unforseen circumstances, I am unable to work on migrating my Mastodon instance to Le Potato, but I've still been thinking about Mastodon and the broader Activity Pub ecosystem. Couple that with a crippling guilt around abandoning an F# project I started a while back, and you have the impetus for my latest project: a Mastodon client written in F# using Fable (an F# -> Javascript transpiler).

### Why Incognitum?

So you know why I'm doing this, but why the name "Incognitum"? As it turns out, "Incognitum" was the term used to refer to Mastodons among Europian settlers who had first recorded finding remnants of them. Since this term does have some connection to the term Mastodon, but isn't one of the more obvious mammoth-related terms, it seemed like a good name.

### Getting Started with Fable

The Fable ecosystem, like much of the F# ecosystem, feels a little scattered and unwieldy. The documentation for Fable seems to point in a couple of different places in terms of starting points of various types. After trying a couple of the suggested templates myself, I ended up going with a template using [Feliz](https://zaid-ajaj.github.io/Feliz/) - a Fable-based React wrapper. This was mostly so that I would have something familiar (React) to draw some knoweledge on as I dive into this new F# world.

Getting the template installed and set up took two quick commands:

```
dotnet new -i Feliz.Template
dotnet new feliz -n incognitum
```

The basic template has a couple of components, but only renders one (in Main.fs): `Components.HelloWorld`. The first thing I did was swap that component out with `Components.Router`. This "Router" component sets things up so that the "index" route ("http://localhost:8080/") renders the word "Index". But it also exposes two other routes "/#/hello" and "/#/counter" which render the "Hello World" component and a cannonical "Counter" component respectively.

Now maybe I should have just read more carefully, but it took me a while to realize that the "/#/" was critical to the way that Feliz does routing by default. Once I dug a little further, I realized there were some alternate options, but I decided to leave them that way for the time being.

_This would turn out to be a mistake._

### Making Contact with the Fediverse

With the basics all figured out, my next step was to figure out what would be my first point of contact with the Fediverse. It turns out that Mastodon is not unlike many other REST API's in that it expects you to first authenticate your application with it, before you can do literally anything else. This gives me a natural first step: [adding an OAuth application.](https://docs.joinmastodon.org/client/token/)

Where Mastodon does differ from many other REST API's is that it does allow for an automated registration of your application with the API. This means that you don't have to get some client id/secret pair set up via some developer portal, but instead you can simply register your application using an HTTP request. Logically speaking, Incognitum will eventually need to keep a store of which Mastodon instances it is registered with and what it's credentials are for each. For now, I decided to see if I could simply register an application for each "session", or reall just register an application at all. 😅

In order to kick this workflow off, I started by replacing the MainPage component with a very simple HTML form. Though it isn't _semantically_ a form, it is composed of a label, an input, and a submit button, so we'll call it "close enough".

```
dotnet new -i Feliz.Template
dotnet new feliz -n incognitum
```

The basic template has a couple of components, but only renders one (in Main.fs): `Components.HelloWorld`. The first thing I did was swap that component out with `Components.Router`. This "Router" component sets things up so that the "index" route ("http://localhost:8080/") renders the word "Index". But it also exposes two other routes "/#/hello" and "/#/counter" which render the "Hello World" component and a cannonical "Counter" component respectively.

Now maybe I should have just read more carefully, but it took me a while to realize that the "/#/" was critical to the way that Feliz does routing by default. Once I dug a little further, I realized there were some alternate options, but I decided to leave them that way for the time being. Below is the code and what that looks like:

![F# code for the Main Page component](/images/2022/11/21/fable-masto-figure-1.png)

![A simple HTML form with the label 'Instance', a text input, and a submit button](/images/2022/11/21/fable-masto-figure-2.png)

With the form set up, it was time to wire up an HTTP request, per the Mastodon documentation. My first instinct was to use a utility I had used once before, FsHttp. After pulling in the package and getting a strange error, it dawned on me: this code ultimately compiles down to Javascript, so I can't use FsHttp (which I'm fairly sure uses .NET's HttpClient under the hood). After consulting the Fable docs again, it became evident that I would need to use Fable.Fetch or Thoth.Fetch (which wraps Fable.Fetch). This is where things went fairly off the rails...

I'll summarize here, mostly because re-living the neightmare that was getting a simple post request working would be detremental to my overall wellbeing. I started off using Thoth.Fetch, which combines Fable.Fetch with a nice API for JSON serialization/deserialization. The api was pleasant enough and everything was going well. Once I had everything wired up, I made a request and got a 422 status code back. Now, the 422 status code is an expected one, per Mastodon's documentation. However, it mentions that you should receive an error message along with that status code - DevTools showed no response, just the status code.

Given that I wasn't getting an error message at all, I thought I had broken something with the request that I made. Looking around, it _seemed_ as thought Thoth.Fetch wasn't actually sending an JSON with the requests I was making. So I decied to abandon Thoth.Fetch in favor of using Fable.Fetch directly. **Big mistake.**

I fought with the Fable.Fetch api for hours, eventually abandoned it, came back to the Thoth.Fetch implementation, noticed that JSON _was in fact being sent_, but that Mastodon was always returning 422. With no error message to point me at what was going wrong, I just decided to start tweaking properties on the request until suddenly, I got a 200 OK. In fact, I had gotten back a set of credentials that I could presumably use to start querying Mastodon's API further.

The culprit? I had attempted to use the Feliz routing system to pre-emptively generate what would eventually be a valid redirect url. If you recall, Feliz uses "hash-based" routing by default, so my redirect url looking something like "localhost:8080/#/oauth/redirect". The request that succeeded had a redirect url that looked like "https://test.thewebres.com," on a hunch, I decided to remove the "hash" from the generated URL and... success!

At this point, it was clear that I would need to change the routing mode for Feliz, so I followed the instructions laid out in the documentation. Meaning, I added the "router.pathMode" argument and swapped out some usages of various methods that were specific to the "hash-based" routing. Having swapped everything out, I noticed a new problem - I couldn't navigate directly to any of the other URLs anymore. Unfortunately, this sent me down another rabbit hole, that had a simple enough solution: [add "historyApiFallback: true" to your webpack devserver config.](https://github.com/Zaid-Ajaj/Feliz.Router/issues/17)

### Wrapping Up

With all of those shenanigans out of the way, I finally have the first small piece of my client working. Users can go to the page, type in the domain of their Mastodon instance, and the app will reach out to register itself and get back a set of credentials. Here's the code for the Main Page at this point:

![F# code for the Main Page component, with a working HTTP request](/images/2022/11/21/fable-masto-figure-3.png)

The plan now, is to tackle two problems next: storing the credentials and pulling a user's account information, but that will have to wait until next time.
