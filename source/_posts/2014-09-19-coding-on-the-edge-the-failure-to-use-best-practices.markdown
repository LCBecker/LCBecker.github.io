---
layout: post
title: "Coding on the Edge - The Failure to Use Best Practices"
date: 2014-09-19 09:17:50 -0400
comments: true
categories: TDD agile refactoring
---
{% img center /images/badcode.jpg 'bad code cartoon' 'bad code cartoon' %}
###### wtf - code quality measurement by [smitty42](https://flic.kr/p/4qquBv) ######

### Some Background ###
For two semesters, my programming professor (then head of the University of Pennsylvania's MCIT program) taught me a number of Agile techniques -- unit testing, coding standards, refactoring, TDD, and pair programming. Since I hadn't really done any programming before entering MCIT, I didn't have any bad habits that needed to be unlearned. I do know that I did not follow TDD as well as I should (write tests before writing the method? Impossible!), but I slowly adapted the right way to do TDD.

During my summer internship, I was surprized to learn that no one seemed to write any tests for their code. To be fair, I didn't either, because I didn't know exactly how to test HTML and JavaScript without actually starting up the website and verifying its look and behavior. My boss exclaimed, "People don't actually do test driven development in industry."

I'm currently taking a software engineering course, which focuses on how to measure if a piece of code is "good." It's an entire class about software development best practices. Many articles I've had to read for the course all have an interesting theme through them. "But [author's name here], what if my company doesn't practice Agile development?" The answer: Do it anyway.

### Some Statistics ###
I wanted to know if my former boss was right. Does industry NOT follow TDD? So, I found a 2013 survey called [State of Agile](http://stateofagile.com/8th-annual-state-of-agile-form/) from VersionOne. Now given, VersionOne makes a product to help companies adapt Agile techniques, so I'd say the results are skewed when the report says 88% of respondants say their company practices Agile development. But what's more interesting is what techniques those companies that do Agile development actually use:

- 85% have a Daily Standup meeting.
- 72% utilize unit testing.
- 47% refactor their code.
- 38% practice Test Driven Development.
- 30% do pair programming.

So it seems my former boss was right! TDD is not popular, despite it's obvious benefits ("Using TDD, you are never far from working code…").

### Some Inferences ###
The statistics are what each company openly practices in terms of Agile techniques. That doesn't mean that their developers aren't doing things like refactoring and TDD on their own! After all, these developers are supposed to be professionals. They probably do incorporate best practices to do their work (at least, we can hope so!).

We can also infer that some Agile practices are more beneficial than others. While I have done pair programming in the past, it's not a big deal if a company doesn't embrace that technique. It's great to get a new employee up to speed on a piece of code and add new functionality to it, but it's not a necessity.

Needless to say, I am a firm believer of good practices. Anyone who has me on their team will know that the code I produce has been tested, refactored, and written with a great deal of attention, care, and dedication.

### The Takeaway ###
All developers should be aware of key Agile techniques and incorporate them into how they code. Not testing or refactoring your code is a very, VERY bad idea. Those 60+ hour weeks might very well be the result of poor coding practices, which, as _Business Insider_ explains, may be driving you and your co-workers [crazy](http://www.businessinsider.com/syndromes-drive-coders-crazy-2014-3).  