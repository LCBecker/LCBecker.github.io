---
layout: post
title: "The Right Stuff"
date: 2016-06-29 17:53:22 -0400
comments: true
categories: Play Framework
---
Building applications requires time, thoughtful planning, and the right tools to complete the task. There are so many programming languages and frameworks to choose from that sometimes I think people are overwhelmed by the "possibilities."

At work, we use Scala and Play Framework. Normally, those who come from a Java background are supposed to be amazed by the versatility of Scala. I come from a Java background. I am not impressed. I don't _hate_ Scala, but it's a struggle to use because it is poorly documented. The massive amount of collections, both mutable and immutable, is not a feature. I'm constantly using the REPL to determine how to add, delete, and retrieve values from the whole host of Scala collections. It's not intuitive. Scala is like an elitist programming language. It's like its creators decided, "Oh, you'd like to learn Scala? That's great! Best of luck to you!" and then idly stand by and laugh as you try to make sense of it all. I even own a book on Scala and even it is ambiguous at best on many "features" of Scala.

Now on to Play Framework, which is even worse than Scala itself. I'm pretty much a pro at application frameworks. I've used Ruby on Rails, Dropwizard (Java), Django (Python), Node.js, and probably a few more that I'm forgetting right now. I'll admit that Play's backend functionality...you know, models and controllers, are rather straight forward. If you actually know Scala or Java (though Play seems to discourage using Java), you're all set. But when it comes to writing a front-end? Good luck!

I spent almost 2 weeks on building a view that should have taken 2 days. I wanted a rather simple form and Play's Form generation seemed like the way to go. It has validation on the backend, serializes to a model, and all that good stuff. Unfortunately, many use cases just do not conform to Play's idea of a form. I wanted radio button displayed vertically. Play said NO. I wanted radio buttons that looked like buttons (no circle to click on...instead, the button background should change when clicked). Play has no means of doing that. It says you can create your own inputs, but that's in the _singular_ and obviously, I wanted multiple radio buttons. I asked for help on StackOverflow and even in the Play Framework Google group. __CRICKETS.__ If you use this framework, you are on your own to figure out how to use it. The documentation lacks full descriptions of how to do basic things and there's no community for help.

In the end, I was put out my misery and wrote the front-end in pure HTML with some jQuery and Parsley for validation. And it took me barely a few hours to get everything working! Just because something is there doesn't mean it's the right tool. Sometimes, if you know something is just easier to work with, saves time, and the result will be easier to maintain in the long run, that's what you should choose.
