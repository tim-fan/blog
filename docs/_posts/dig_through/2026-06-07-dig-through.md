---
layout: post
title: Dig Through
date: 2026-06-07 12:30:00 +0000
---

Living overseas, it's nice to have a sense of where your home country is relative to your current location.

Presenting: 🌍 **Dig Through!** 🪏

Uses WebXR + your geolocation to display a virtual hole dug into the earth, to show what's on the other side.

<figure>
<video controls width="50%"><source src="https://pub-e97d8798980d424ab1af22fba84810e3.r2.dev/1780864306_Screen_Recording_20260607_125729_Chrome.mp4" type="video/mp4" ></video>
<figcaption> Demo video, digging through the earth to find New Zealand
</figcaption>
</figure>

### You can try it too 👇
It's browser based, you can try it on your phone:

[https://tim-fan.github.io/dig-through](https://tim-fan.github.io/dig-through)

You'll need to grant location permissions for it to work.

### Details

This was a concept I tried a couple years ago but didn't complete. Now I've tried again with the power of AI. This was mostly coded on my phone with claude while I was on the train. Still needs clean up but it mostly works to show the concept. 

Code is here:

[https://github.com/tim-fan/dig-through/tree/main](https://github.com/tim-fan/dig-through/tree/main)

### ToDo items
* Confirm accuracy - the direction to New Zealand is roughly as I expect, but should be more methodically tested
* Nicer effects, show a little spade digging the hole, sound effects, better textures
* Select satellite or map view
* User flow improvements - graceful error handling, graceful handling of location permission denial, etc
* Should earth model be made much larger? Or scale is negligible?

Obligatory mention to Google's [Floom experiment](https://experiments.withgoogle.com/floom) - that is also a webxr app for seeing through the earth, I just changed the projection approach so you can see full countries and interact by walking around the portal.