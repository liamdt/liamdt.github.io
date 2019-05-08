---
layout: post
published: true
title: Assignment 7
date: '2019-05-08'
---
## The Post Awakens

Here's a screenshot from that Codecademy goodness.
![Codecademy78.PNG]({{site.baseurl}}/img/Codecademy78.PNG)
Nice!

Now let's talk about webscraping.  

For a while in college, I lived in a basement room, and there was a crawlspace that surrounded the room.  It was an old Minnesota basement, and was probably some form of insulation.  But it was filled with cobwebs.  This is irrelevant except in my nightmares.

For the task in question I followed the following steps:

1. Found the full XML of an issue as opposed to a single article.  This was aided by you directing us straight to them.

Then two methods were used:
2. Checked a number of them and found that they followed a simple sequence.
3. Checked the last one to find out how high the numbers went.
4. Dragged in excel to increment the URLs by one up through the end.
5. Unleashed wget on the full list of URLs

As noted, this could fiddle my sticks if there's a break in the list.  Thus option two, the superior choice.

2. Compare the XML URL and the page URL, finding the difference to by a "dl" snuck in there.
3. Copy and paste all of the page source into Sublime.
4. Use Regular Expressions and General Courtesy to strip away everything but the URLs, as well as finding and replacing in order to complete the URLs/putting that dl in there
5. Sent wget hurtling into the night