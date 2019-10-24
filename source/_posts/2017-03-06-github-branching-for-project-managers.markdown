---
layout: post
title: "GitHub Branching For Project Managers"
date: 2017-03-06 21:28:20 -0500
comments: true
published: false
categories:
---
As a project manager, you may wonder about this thing called GitHub (or Git) that your developers talk about (constantly). This post is for you. It will explain in simple terms what GitHub is and how your software engineers use it. Key terms will be either italicized or bolded. Hopefully by the end of this post, you'll have a better understanding of how software engineers write, store, test, and deploy their applications (code).

Code exists in 3 places for most developers. You know code best in its running state. You probably go to websites like stage-our-application.companyname.com. These websites are really _environments_ where the code developers write becomes active and usable. The code is _deployed_ there from GitHub.

GitHub stores files, just like Google Docs, remotely on its servers. However, GitHub has a special purpose called **version control**. GitHub not only stores the contents of files, but remembers what changed and when. This means that if a bad change occurred that breaks something, we can reverse those changes to a previous stable, safe version.

The final place code exists is on each developer's computer. Developers _pull_ (retrieve) the most up-to-date code from GitHub and save it to their local computer hard drive. It is here that they make their additions and changes. Then, when it's ready, they _push_ it (upload it) to GitHub. _Git_ is the process of managing those local files.

Incremental additions and changes are known as _commits_. Commits are like snapshots--how things look at a particular moment in time. For example, if I fix a bug, that's a commit. I'm saving the way every file looked after I made my changes to fix the bug. If I add a new feature that involves many different changes, such as deleting one webpage and adding a new one, I might make multiple commits.

Code and commits are a bit like geology. There are many layers of rock, building upon one another. The same is true for code. If I make a change, I add a commit on top of other commits (changes and additions) that other developers have made previously. They stack on top of one another.

Git branches are a way of organizing code. Branches exist in GitHub and are continuously copied and refreshed locally on a developer's computer. Usually, you'll have a development branch, a staging branch, and a production branch. The development branch has the most up-to-date code, though there may be things that still need to be ironed out or cleaned up. Once a developer has completed a
