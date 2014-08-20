---
layout: post
title: "Play Framework for Dummies"
date: 2014-08-19 14:44:12 -0400
comments: true
published: false
categories: play
---
This super short overview is for those of you who want the basics of Play in Scala. All steps are for OSX users.

###### Prerequisites: ######
* You must have Play Framework already installed.
* You must know how to use Terminal.
* You must have Scala installed.
* You must have Scala IDE for Eclipse installed.
* You must have a basic (and I mean _basic_) grasp of Scala.
* (Optional) You might need a pillow to scream into because this is web development and it's frustrating.

#### Create a new project ####
1. Open Terminal.
2. Navigate into the directory you want your new project to live.
3. Type `play` into Terminal. This will start Play.
4. Type `play new projectname` where `projectname` is the name you want to give your project (um, yeah, of course, like that needed explaining!).
5. When Play responds with `What is the application name? [projectname]` just hit `Enter`. You've already named it. Play is stupid.
6. When Play responds with `Which template do you want to use for this new application` press `1` for a Scala application. Because even if you chose a Java application, you'd still have to code in Scala at some point.
7. Ok, your project is created. Have fun (crying into your pillow)!

#### Setting up Play with Eclipse (because only losers use IntelliJ) ####
1. In Terminal, navigate into your new project's directory.
2. Type `play` into Terminal. You need to start Play…again.
3. Type `eclipse` into Terminal. Watch as Play generates a bunch of files to make the project (barely) usable in Eclipse.
4. Open Scala IDE for Eclipse.
5. From the top menu bar, select File -> Import.
6. In the window that pops up, under the General folder, select "Existing Projects into Workspace."
7. Make sure `Select root directory:` radio is selected. Click the `Browse` button and find the your project's _folder_. Don't go into it. Just highlight that folder and click `Open`. It should appear in the `Projects` area.
8. Press the `Finish` button.
9. You should now see your project appear in the `Package Explorer` area. You're ready to begin working on your project in Eclipse now.

#### Running your new project ####
1. In Terminal, navigate into your new project's directory (if you haven't already).
2. Type `play` into Terminal (if Play isn't already started).
3. Type `run`. You'll get a message like: ` [info] play - Listening for HTTP on /0:0:0:0:0:0:0:0:0:9000 `.
4. Open your internet browser of choice. Go to localhost:9000.
5. You should get a Welcome to Play! site appear. It looks so nice and friendly. That's how Play Framework pretends to be helpful. Do you have your pillow nearby?

#### Understanding the Components ####
In the `app` folder, you've got two packages: `controllers` and `views`. Click on the `controllers` package and open Application.scala.

###### Application.scala ######
This is a Controller. Brush up on [MVC](http://en.wikipedia.org/wiki/Model%E2%80%93view%E2%80%93controller). Currently, this controller does one thing. It sends an HTTP response to the browser saying everything's cool using the `Ok`(because giving status code 200 would be make things more intelligent, but…ok) and it sends a string over to the view (the thing with some HTML in it). 
