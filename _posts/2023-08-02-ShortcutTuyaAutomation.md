---
permalink: /posts/DitakticBot
title: "Automation of Tuya with Homekit and Shortcuts"
tags: Shortcuts Homekit Tuya SmartHome
---

Problem: I have devices that are registered and linked into my Tuya app, but HomeKit on iOS doesn't yet support integrating them into my Home app. While I can automate them through the Tuya app itself, Shortcuts is a bit more powerful and I'd like everything in one place. So, how can I link these?

Over the past few months - since moving into my new apartment - I've acquired some new electronic devices, including a vacuum and some air conditioners. The air conditioners themselves are not smart, but in combination with a smart IR device, I can use an app to turn them on and off. These devices are all supported by Tuya, but are not yet supported by Apple's Homekit.

Over the course of a fair bit of research and trial and error, I found that from the Tuya app it's possible to create Tap-to-Run scenes that can be exported to Siri. For example, say I want to turn my Air conditioner on. I create a Tap-to-run scene in Tuya with the actions I want (turn on air conditioner) and I use the "add to siri" button to add a simple shortcut.

This creates a voice activated shortcut that can trigger a Tuya scene. In addition, by going into the shortcut, selecting the down arrow on the scene action, it's possible to disable the confirmation/ see when run option, making things smoother. Also, it's possible to create some extra logic and automation around this. For example, my air conditioner scene doesn't actually distinguish between on and off. With the addition of some clever logic with the app Data Jar, I can store the state on my phone. (it's not perfect, as it will fall out of sync if I update it outside of Apple).

Now things are set up to create scheduled or other automations. Create a automation (e.g. run at time X) like usual. As a part of this, include the command "Run Shortcut" and target the shortcut that was created in the previous step. This work-around may seem a bit silly, but it's the only way I could find to accomplish this. In addition, if there is extra logic in the original shortcut, that gets encapsulated by this and handled automatically.

In short, using the Siri shortcut created from the Tap-to-run scene in Tuya, one can activate it with a "Run Shortcut" command in another automation.

