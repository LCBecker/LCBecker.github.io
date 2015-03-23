---
layout: post
title: "Reinventing The Wheel (or Web Server)"
date: 2015-03-23 09:10:53 -0400
comments: true
categories: 
---
My current (and final) course before I graduate has given me a new appreciation for Java packages. As developers, we use them all the time. In some cases, the more packages you know about, the more efficient and readable your code may be and the more sanity you get to keep.

My appreciation for packages has grown because the assignments I've had to do for this course have basically asked me to build a number of things from scratch:

- A web server
- An HTTP client
- A thread pool
- A servlet container
- An XPath parser
- An matcher between an XPath and an XML document.

Of course, someone has already written the code to do all of these things in Java. It's either already built into Java or you can download a jar file to use it. But try writing these things from scratch and you'll find that you begin to write some of the nastiest code you've ever written. Classes become horribly coupled, but that's the only way to make the application work. For writing your own servlet container, the API that describes the HttpRequest and HttpResponse interface methods is lacking. But it will make you appreciate Tomcat and all the work that went into making it.

It's amusing that as a computer science student, I have been given a repetoire of principles to code by - DRY, KISS, YAGNI - and most of my assignments force me to violate them one way or another. Sure, writing certain functionality from scratch to really understand how things work under the covers is a great learning experience. But it's hardly simple when I could just import a package and use the classes/methods within it. Reinventing the wheel (or the web server, or the XPath parser, etc.) is a lot trickier than you'd expect.