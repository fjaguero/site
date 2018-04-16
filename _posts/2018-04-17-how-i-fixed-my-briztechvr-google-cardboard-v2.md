---
layout: post
title:  "How I fixed my BrizTechVR Google Cardboard V2 lens issues"
date:   2018-04-16 01:15:49
tags:   Cardboard VR
---

![Cardboard](/content/images/2016/04/cardboard.png)

This week I ordered the [BrizTechVR Google Cardboard V2](http://www.amazon.co.uk/dp/B00Z6SIARQ/ref=sr_1_1?ie=UTF8&qid=1428254133&sr=8-1&keywords=google%20cardboard&m=A12O1TAXK1QT74). These are not the original [Google Cardboard](https://www.google.com/get/cardboard/) but since this is basically _cardboard_, the guys from BrizTechVR followed the [specifications](https://www.google.com/get/cardboard/get-cardboard/) for both the first and the V2 version.

You can't expect a incredible quality _tech_ product for 20€, but the improvments from the first version are worthy (there are [some <30€ non-cardboard](http://www.hypergridbusiness.com/2016/03/review-lenest-viewer-is-close-to-perfect/) versions with really good reviews). The V2 version now it comes with a push button (no more magnets) and it's ready to handle large screen devices.

### The problem
**The QR code that set the lens config wasn't working properly**

I tried them on both Android and iOS to check the marketplaces and see what was available. At the end, the [Cardboard](https://play.google.com/store/apps/details?id=com.google.samples.apps.cardboarddemo&hl=es) app has some good recommendations. My favorite one is [VRSE](http://vrse.com/). It's not a game but a platform for VR storytelling.

I started watching the NYT _Walking in New York_ [documentary](http://vrse.com/watch/id/100/) but suddenly what I was viewing **was wrong**. It wasn't correctly centered. I was able to correctly view the video by closing one eye... so I guessed that the QR code that has the configuration settings wasn't working properly. Actually, I tried some other games and it worked perfectly.

## The solution

**Creating a custom lens config did the trick**

![Viewer Profile Generator](/content/images/2016/04/Captura-de-pantalla-2016-04-03-a-las-3-09-30.png)

Turns out that [I wasn't the only one](https://www.reddit.com/r/GoogleCardboard/comments/3ed1b3/double_vision_with_cardboard_app_incorrect/) having this kind of problems.

All the software that uses the [Cardboard SDK](https://developers.google.com/cardboard/) are able to change the lens config. This allows everyone to make his own cardboard and then just generate a **Viewer Profile**.

To generate the [viewer profile](https://www.google.com/get/cardboard/viewerprofilegenerator/) we need to pair the phone and the browser by accessing an specific URL that the profile generator give us.

![Profile generator](/content/images/2016/04/profile-generator.png)

Basically I changed the main parameters like this:

![Config](/content/images/2016/04/Captura-de-pantalla-2016-04-03-a-las-21-49-35.png)

And finally... the generated QR code if you want to try it:

![QR code](/content/images/2016/04/qr_viewer_profile.png)

Never settle.



