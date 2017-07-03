---
layout: post
title:  "React + Redux: Final Project Review"
date:   2017-07-03 03:05:53 +0000
---


Whoa! Last project! When I first started this course, I expected the final project to be a giant and complete review of everything I learned. While it was a review, I also tackled a few new ideas. The resulting application seems a lot simpler than some of my previous work - especially my Rails/jQuery project. My final project is a single page application with a Rails API for the backend and a front end created with React and Redux. It is a lunch restaurant finder/recommender for Charleston, SC (where I'm from!). My backend pulls local data from the Yelp Fusion API. On the front end, the user can see a list of restaurants open for lunch in downtown Charleston and filter the list of those restaurants by price, location (zip code), and if they allow takeout. The user can also get a recommendation for a lunch restaurant and view a map in the browser with the restaurant's location. 

As I said, this is a simple application. There are no logins or user profiles. It is open to everyone. My Rails backend does not really manipulate the Yelp data the way my Rails project did. Also, there are no forms allowing the user to add new information to send to the database. When you look at it, it is only two main pages - the list and the recommendation. That being said, I'm still proud of the product I created because I struggled and pushed myself to go beyond the Learn lessons to implement these few features. 

I plan on writing two future blog posts describing my steps for creating filters and adding a map feature. Both of these are extremely common on other websites, but I found them pretty difficult to implement first time around. With the map feature, I ran into simple road blocks that took a TON of time to fix because I was using an external module that I didn't fully understand. For the filters, I was not sure how to go about creating them in the first place, and even once I understood what to do, the logic I implemented was very long and would be difficult to extend to additional filters. I'm glad I got them to work, and I look forward to talking to people with way more experience than I do, because there must be a better way!
