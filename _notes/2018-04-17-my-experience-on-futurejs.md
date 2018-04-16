---
layout: post
title:  "My experience on FutureJS"
date:   2018-04-16 01:15:49
tags:   Personal Javascript
---

### The learning process

I started in _Aluana_ in November and since then I’ve learning a lot. Quite a lot. In fact, I think that I learned more in the last 6 months that in the last 2 years. 

At the company, we allocate one hour every day for learning, making a total of 20hrs/month plus the continuous learning developers do while programming. Events and conferences are great places to improve our knowledge and, specially, learn about cutting-edge trends and technologies.

<iframe width="560" height="315" src="//www.youtube.com/embed/hslIXaAsR-I" frameborder="0" allowfullscreen></iframe>

Three of us went to **FutureJS** and it was an unforgettable weekend. The overall quality of the talks was high, not bad for a JS event in Spain. One of the main topics of the event was the reactive programming as an answer to the DOM overload and performance problems.

My personal highlights, in no specific order, are the following:


### Pete Hunt: The Secrets of React's Virtual DOM

<iframe width="560" height="315" src="//www.youtube.com/embed/-DX3vJiqxm4" frameborder="0" allowfullscreen></iframe>

We were wondering what Pete was going to tell us about [React](https://github.com/facebook/react). The talk was entertaining, with different demos and performance charts.

We tested the library before and it feels great. The Virtual DOM is considerable improvement for the UI performance. The drawback here is that the modular structure we want to accomplish with our product is not compatible with the way React handles views, specially the flexibility to move the templates out of the logic.


### Martin Naumann: Web components - A whirlwind tour thru the future of the Web

<iframe width="560" height="315" src="//www.youtube.com/embed/_tNj42SCtsY" frameborder="0" allowfullscreen></iframe>

Martin talked about the flexibility of the Web Components and how the Web is turning into a flexible shape. Good news is that this flexibility is “standarized”. This will make web development more modular and speed up development.

**Special mention: +1 to his Firefox OS t-shirt.**

### Jeremy Ashkenas: JS.Next and CoffeeScript

<iframe width="560" height="315" src="//www.youtube.com/embed/TiLJKpzlOeY?list=UUwoOpKfkyCQHW562hXXQAGg" frameborder="0" allowfullscreen></iframe>

Jeremy is the creator of [Backbone.js](https://github.com/jashkenas/backbone) and [CoffeeScript](https://github.com/jashkenas/coffeescript), making him one of the big stars of the event.

The talk was mainly focused on the future of Javascript and the benefits of CoffeScript to embrace this changes and still output compatible code (lowest common denominator, ES3/ES5). Some of the code demos were ES6 features that already exist in CoffeScript... always taking the workaround to let it work on all the browsers.

**One interesting question from the public was: "Will CoffeeScript be updated to the new ES6 features?"**

The answer from Jeremy was the following:

"In my world, you have to write code on machines that people use [...] even supporting IE8. The most important thing is to have a tool that would not force you to do workarounds if you wanna make your code run across the board. CoffeScript takes this things that are in ES5 and ES6 and compiles it down to lowest common denominator ES3 so it's in a way that is compatible."

He then said **"ES6 will introduce features that already existed in CoffeeScript or that are in conflict with CoffeScript features and so the code will suddenly not work correctly"** and finished by stating that CoffeScript will introduce the ES6 (not as fallbacks) when all the browsers support it. Other things like Yields and Generators is something that they should be adding now because that won't have kind of conflicts.

The rest of the talk was about the possibility to have a common language in the future: a mix of the different layers (semantic, presentation and interaction) in a single, optimised and flexible one.

### Jaume Sánchez: Everybody dance now with Web Audio API

<iframe width="560" height="315" src="//www.youtube.com/embed/Rb3AAQ-xDjQ?list=UUwoOpKfkyCQHW562hXXQAGg" frameborder="0" allowfullscreen></iframe>

I know that the Web Audio API may not “sound” too futuristic, but the things that you can do in the browser are amazing. Is not common for a web developer to play with the Web Audio API, so many of us didn’t know all the complex things that can be done. 

Jaume's speciality is this API, he developed the sound integration for the webpage of the movie *Gravitiy*. He showed us an interactive demo, playing with different filters and configurations. It was really easy to follow and everyone learned a lot in just 50 minutes. The complexity was increasing as he played with different filters, finishing with the connection of the different inputs and a mutiplexer.

**UPDATE:** Firefox 32 now has a Web Audio API development tool panel
![Firefox 32 Web Audio API](https://hacks.mozilla.org/wp-content/uploads/2014/06/complex_webaudio_graph.png)


### Ben Foxall: A conceptual future for the multi-device web

<iframe width="560" height="315" src="//www.youtube.com/embed/1mkShXn_buA?list=UUwoOpKfkyCQHW562hXXQAGg" frameborder="0" allowfullscreen></iframe>

Ben is famous for doing this kind of interactive talks where everyone can participate. The mind-blowing part here was to show a little piece of a huge image to every connected device on the room. While he was moving the image, the position was updated each device. This showed the potential of the Web as a platform.

## The FutureMeter

![FutureMeter](http://cl.ly/image/1e0l2g0B2x3M/Image%202014-08-18%20at%207.50.51%20p.%20m..png)

After an entire day of demos about performance, reactive programming and real time, we were eager to do some fancy Javascript. That night we had a idea that we wanted to share with everyone at the event: The [FutureMeter](http://futurejs.meteor.com). 

It was a web that showed the number of times that every speaker said the words *today* and *future*. We built it in the hotel room in a matter of hours while drinking. We thought it was going to be fun... and it was!

![Future JS](http://cl.ly/image/1f3o1Z463203/photo51769461773019051.jpg)

The organisation featured our Meteor-backed application on the main screen between every talk and even some of the speakers mentioned the meter. Pete Hunt was aware that he was saying *future* too much and then he said something like: “Ok, I will break the future meter”. Even Martin Nauman was joking with the words while doing his demo with Web Components.

**Long live JavaScript conferences.**
