---
title: Using MutationObservers to build a simple extension
date: 2018-04-16 01:15:49 Z
tags:
- Javascript
- Github
- Extension
layout: post
---

Have you ever have the need to _listen_ to DOM changes? I did. That's why I built a simple extension by using *Mutation Observers*. Let's get some context.

### DOM mutations APIs
Listening to DOM changes was possible with the past DOM3 specification, but it didn't have good, specific API: you needed to use event listeners.

##### Previously on DOM3

This was called _Mutation Events_ and it exposed a list of events you were able to listen to:
```
*  DOMAttrModified
*  DOMAttributeNameChanged
*  DOMCharacterDataModified 
*  DOMElementNameChanged
*  DOMNodeInserted
*  DOMNodeInsertedIntoDocument
*  DOMNodeRemoved
*  DOMNodeRemovedFromDocument
*  DOMSubtreeModified
```

Usage example:
```language-javascript
element.addEventListener("DOMNodeInserted", function (ev) {
  // ...
}, false);
```
---
#### Meet the DOM4 Mutation Observers
Now we have a better API in the [DOM4](http://www.w3.org/TR/dom/#mutation-observers) specification, **MutationObservers.** This specification became a W3C recommendation on 19 November 2015.

[MutationObservers](https://developer.mozilla.org/en/docs/Web/API/MutationObserver) has a more complex API that the previous method, but it's specifically designed for the requirements. The good thing here is that you specify which DOM changes you will be _observing_ when instanciating the observer.

In my case I will be observing the DOM mutations when the subtree is changed.

An implementation example:

```language-javascript
// select the target node
var target = document.querySelector('#some-id');
 
// create an observer instance
var observer = new MutationObserver(function(mutations) {
  mutations.forEach(function(mutation) {
    console.log(mutation.type);
  });    
});
 
// configuration of the observer:
var config = { attributes: true, childList: true, characterData: true };
 
// pass in the target node, as well as the observer options
observer.observe(target, config);
 
// later, you can stop observing
observer.disconnect();
```


###### Browser support

It's expected that IE 11 is the only version that supports MutationObservers, since they already stated that it will be the last support version. The new versions of Edge already supports this feature, as well other modern browsers.

![Browser Support](/content/images/2016/02/mutation.png)

### Building a PoC extension

I built a small [Chrome extension](https://github.com/fjaguero/twitter-replies-counter) (*port to WebExtension soon, they ship on Firefox stable on March 2016!*) that simply counts the number of replies a tweet has. This is done by observing a div (the twitter popup) and counting the number of subtree modifications (each modification is a reply).

**Extension example usage**
![Extension](https://camo.githubusercontent.com/0ac2c364825f373503b8220879c51424e5a67b95/687474703a2f2f636c2e6c792f334f324a336830733342325a2f53637265656e2532305265636f7264696e67253230323031362d30322d3237253230617425323030342e3134253230702e2532306d2e2e676966)

**Content script**

This script is injected when the page is loaded:

```language-javascript
$(document).ready(function() {
  // Select the target node (tweet modal)
  var target = $('.PermalinkOverlay-modal').get(0);

  // Create an observer instance
  var observer = new MutationObserver(function(mutations) {
    mutations.forEach(function(mutation) {

      // Get the Twitter modal window replies count
      var loneTweetsCount = $('.PermalinkOverlay-body .ThreadedConversation--loneTweet .tweet').length
      var threadedTweetsCount = $('.PermalinkOverlay-body .ThreadedConversation .tweet').length
      var total = loneTweetsCount + threadedTweetsCount

      // Send message to background.js so we can set the badge text
      chrome.extension.sendMessage({'count': total})

    });
  });

  // Configuration of the observer
  var config = { attributes:true, subtree: true };

  // Pass in the target node, as well as the observer options
  observer.observe(target, config);

});
```

Note that I'm using `chrome.extension.sendMessage` so I can send a message to the background-script. The background-script is able to use many of the browser APIs that are not available on the injected script (content-script).

**Background script**

On the background-script I listen to that message and change the badge text. That's all.

```language-javascript
// Listen to content.js events
chrome.extension.onMessage.addListener(
  function(request, sender, sendResponse) {
    if (request.count || request.count === 0) {
      chrome.browserAction.setBadgeText({'text': request.count.toString()})
    }
  }
);
```

It's great to see how the browsers APIs gets more and more powerful and easy to use... keeping in mind that the Web can run anywhere!

Never settle.
