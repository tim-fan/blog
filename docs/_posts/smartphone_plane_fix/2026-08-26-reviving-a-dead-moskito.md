---
layout: post
title:  Reviving a Dead Moskito
date: 2026-08-26 12:30:00 +0000
---

From a neighbor:
> We have this old smartphone controlled plane but the app isn't on the app store anymore - can it be saved?

![product_14623_product_shots2_image.jpg](https://pub-e97d8798980d424ab1af22fba84810e3.r2.dev/1787802070_product_14623_product_shots2_image.jpg){: style="aspect-ratio: 630 / 473"}

We can try!

Quick search with claude revealed the app code is [open source](https://github.com/TobyRich).

Claude also suggested a test connecting with [nRF Connect for Mobile](https://play.google.com/store/apps/details?id=no.nordicsemi.android.mcp) for connecting to BLE devices.  On connection I saw two advertised services:

<img src="https://pub-e97d8798980d424ab1af22fba84810e3.r2.dev/1787803129_Screenshot_20260826_204950_nRF_Connect.jpg" alt="nRF Connect" width="50%" style="aspect-ratio: 1080 / 1779"> 

I wrote `255` to the first service, and the propeller moved. Easy!

.. or so I thought. After propeller start the device seemed to lose connection and stop responding. Also it would only power up when usb was connected. I suspected a dead battery. 

Claude suggests isopropyl alcohol to loosen the glue holding the body foam together - that worked and I was able to get to the battery, which was puffy, apparently 10 years old, and measured 0V 😵.

<img src="https://pub-e97d8798980d424ab1af22fba84810e3.r2.dev/1787804266_20260801_160902.jpg" alt="nRF Connect" width="70%" style="aspect-ratio: 4000 / 3000"> 

Found a close-enough replacement on Amazon. Tricky soldering job with my $20 iron, but got it connected and powering up ⚡
<figure>
<div style="display: flex; gap: 0.5rem; align-items: flex-start;" >
  <img src="https://pub-e97d8798980d424ab1af22fba84810e3.r2.dev/1787804705_20260809_083205.jpg" alt="20260809_083205.jpg" style="flex: 1; min-width: 0; aspect-ratio: 4000 / 3000">
  <img src="https://pub-e97d8798980d424ab1af22fba84810e3.r2.dev/1787803632_20260825_193048.jpg" alt="20260825_193048.jpg" style="flex: 1; min-width: 0; aspect-ratio: 4000 / 3000">
</div>
<figcaption>Out with the old in with the new</figcaption>
</figure>

Claude followed up with a static web app for connecting to and controlling the plane (TIL you can make bluetooth connections from the browser). Page is available [here](https://tim-fan.github.io/moskito-controller/).

Then to the successful flight test - it flies well!

<video controls width="50%" style="aspect-ratio: 1080 / 1920"><source src="https://pub-e97d8798980d424ab1af22fba84810e3.r2.dev/1787808044_moskito.mp4" type="video/mp4"></video>

In conclusion - the Moskito flies again 🦟

Nice case of quick+easy hardware hacking with claude as sidekick. 