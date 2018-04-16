---
title: 5 reasons why I choose React-Meteor for my projects
date: 2018-04-16 01:15:49 Z
tags:
- Code
- Meteor
- React
layout: post
---

_This is a small summary of why I am really happy developing apps with Meteor and the react-meteor package._

## 1. Development speed

The main reason of the combo is clear: **speed**. It's crucial to test what you are building as fast as possible. Show your work to people and just ship it™. 

Don't lose time on the all common tasks like the environment setup, build configurations, deploys, login systems, complex data handling, etc.

This is a list of thing that you already have when you start a Meteor project:

- User login/registration (3 lines of code)
- Password change/recovery (5 lines of code)
- Backend authentication
- Database setup
- Easy Deploys
- Hot-reload development
- Data reactivity
- Data sync in real-time

Many of the common tasks are already solved. Just keep one thing in mind: **Don't reinvent the wheel™** . 

There is no need to do all the bootstrapping from ground up... just focus on the core values of what your are building.

A quick example: With [Meteor Up](https://github.com/arunoda/meteor-up/tree/mupx) you can handle the full deploy process without leaving the command... with a single command. 


## 2. Real-time with no config needed

![Websockets](https://media.giphy.com/media/zmRmrZFPoqh6U/giphy.gif)
<span class="caption">Real-time data out of the box. Yay!</span>

With the MEAN stack, when you make a REST request for a table/collection, you're essentially saying "Send me the results of this query". With Meteor, you subscribe to the results of that query, and any newly created or modified documents that matched will be automatically sent to the client over a fast WebSocket connection.

Meteor uses [DDP](https://www.meteor.com/ddp), which basically uses stringified JSONs over WebSocket. It's a simple protocol for fetching structured data from a sever and receiving live updates when the data changes.

What you feel when you have an entire app synced between browsers and users without writing a line of code is amazing.

**There's no need to neither setup anything nor learn a special API!**

## 3. Universal JavaScript

![Meteor Architecture](/content/images/2016/03/meteor-arch.png)
<span class="caption">The Meteor Architecture</span>

All the client and server-side code in the same repo. Same code works on iOS and Android. No context switch, you feel it's all the same.

A small example of both sides:

##### Server code
```language-javascript
// Serve all the user data for our Goals collection
Meteor.publish("goals", function (userId) {
  return Goals.find({
    'userId': this.userId
  });
});
```

##### Client code
```language-javascript
// Using the react-meteor mixin ReactMeteorData
getMeteorData() {
  let sub = Meteor.subscribe("goals")

  return {
    goals: Goals.find({
     'userId': Meteor.userId()
   }).fetch()
  }
}
```

The other good thing is that when you make a change on the backend code, the browser/server is refreshed with the latest changes. Free hot-reload on both sides with zero config (no need for Webpack/Browserify).

## 4. Components magic

![Magic](https://media.giphy.com/media/ujUdrdpX7Ok5W/giphy.gif)
<span class="caption">Using one-way data flow</span>

I don't want to get into detail with React but I can say that it radically changed the way I work with front-end code (like Meteor changed everything I knew when I started using it).

The main wins:

- Declarative code is super easy to reason about
- One way data flows are easy to follow
- It's just JavaScript with a small API

If you are already using React, the only new thing you will have when working with Meteor is that it exposes some methods like `getMeteorData()`, which basically serves you the data over the socket connection. Every time a change is made, your props/state change and the view is updated. Easy peasy.


## 5. Less code/learning curve

The learning curve is low for React: it's just components + state + props. No 300 pages books, no playing with the _best_ way to handle your data for months. 

In the case of Meteor, the learning curve can be higher, but no in terms of code, it's a paradigm change. The main things are `reactivity`, `publications and subscriptions` and how you handle client and server `collections`. Actually the Meteor server-side is a NodeJS on asteroids.

Check this code below.

```language-javascript
Camino.Goals = React.createClass({
  mixins: [ReactMeteorData],

  // Fetch the data. Reactive and real-time by default.
  getMeteorData() {
    let sub = Meteor.subscribe("goals")

    return {
      isLoading: !sub.ready(),
      goals: Goals.find({
        'userId': Meteor.userId()
      }).fetch()
    }
  },

  // Pass the data to the view
  showGoals() {
    return (
      <div className="goals section__container">
        <GoalsList goals={this.data.goals} />
      </div>
    )
  },

  // Show a loading spinner
  showSpinner() {
    return <Spinner />
  },

  // Render the Goals view
  render() {
    return (
      <div className="jumbotron">
        <div className="container">
          {
            this.data.isLoading ? this.showSpinner() : this.showGoals()
          }
        </div>
      </div>
    )
  }

})
```

This is an example of a controller-like view that: 

- Is subscribed to a previously defined data scheme
- Handles the server data in real-time
- Handles changes reactively
- Uses a React component to make the changes on the Virtual DOM, preventing unnecessary re-flows

This means that if any of the clients or the database changes, the view gets updated in every session (browsers, for example) in an efficient way (in terms of performance).

**No extra code needed. Boom.**

![Meteor](https://media.giphy.com/media/YLgIOmtIMUACY/giphy.gif)
<span class="caption">When you realize all the time you lost by not using this amazing combo</span>

---

#### Further readings
- [Meteor To-do app with React](https://www.meteor.com/tutorials/react/creating-an-app)
- [Reactivity Basics: Meteor's Magic Demystified](https://www.discovermeteor.com/blog/reactivity-basics-meteors-magic-demystified/)
- [Meteor performance 101](https://kadira.io/academy/meteor-performance-101)
- [Making Use of Settings.json](https://themeteorchef.com/snippets/making-use-of-settings-json/#tmc-what-is-settingsjson)

_**Update:**
The Meteor Developer Group just released [Meteor 1.3](http://info.meteor.com/blog/announcing-meteor-1.3) which has new great features such as the ability to use ES2015 Modules, a built-in application testing suite, a native NPM integration (on both client and server) among more._


