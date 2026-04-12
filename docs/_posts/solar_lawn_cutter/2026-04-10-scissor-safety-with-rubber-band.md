---
layout: post
title: Scissor Safety with Rubber Band
date: 2026-04-10 12:00:00 +0000
---

The scissors should have enough cutting torque to cut grass, but not enough to, say, cut off a finger. 

To provide a torque limit I added a rubber band here:
<img src="https://pub-e97d8798980d424ab1af22fba84810e3.r2.dev/1775925669_2026-04-11_09-40.png" width="50%" />

This worked in general, but in testing the rubber band extends a little, then the servo hits it's own torque limit and stops moving. Probably the servo torque limit is enough for safety[^1].

Video demo:

<video controls width="50%"><source src="https://pub-e97d8798980d424ab1af22fba84810e3.r2.dev/1775925366_20260410_180611.mp4" type="video/mp4"></video>


[^1]: *relative* safety. I assume the scissors will still give a nasty cut, but at least the applied force is limited. Longer term I'll need some sort of shroud/guard.
