---
layout: post
title:  Thoughts on finishing my JQuery Project!
date:   2017-05-25 10:39:15 -0400
---


Yay me! I just finished up the front end portion of my MyKadima Rails application, and I feel great. More than the past projects, I went into this one feeling pretty lost about jQuery, Javascript, APIs, and what I was doing. I missed Rails, and wondered "Why am I writing all of this code just to save a few seconds on a page reload?". Honestly, I can't say that I'm a convert, but I definitely understand these topics A LOT more than I did going in. First, I want to explain a few hang ups I had during this project, and then I have a few upgrades I want to share, if only to remember how I can grow!

Issue #1: **this** keyword not binding to the Event Target

*TLDR: try using function(e) {} INSTEAD OF (e) => {} to define your function.*

To some, this may seem simple, but I spent hours googling why my $(this) call in my click handlers was not binding to my event target. This made it near impossible to use data attributes I had embedded in my HTML, and therefore, I had trouble getting my URLs to show up correctly (an issue for nested resources with URLs like "/users/${userId}/games"). I realized that I was using the shorthand arrow notation to create my functions in my click handler. With arrow notation, THIS is always tied to your Window instead of your click event. A simple solution to a big issue!


Issue #2: Passing Rails Model Methods to my jQuery page templates

*TLDR: Put it in your Serializer.*

Upon Googling, I found a TON of ways to pass data from Rails to JS including create a custom API endpoint, use data attributes, use <%= javascript_tag do %> in your html.erb file, use the gon gem, use .js.erb files, use the serializer. I didn't know where to turn. I was using data attributes to send little information like my database IDs, but I had a lot of methods to give Users stats on the games they were uploading. I decided to use the serializer to pass information. Most of the stats were on my User Profile (user show page/user model), so I included the method names as attributes in my User Serializer. I also created a few custom methods using the methods from my model to account for some outlier behavior. Lastly, I made my current_user method available to my serializer to help me run my methods. Do this my putting `serialization_scope :view_context` in your Application Controller, and `delegate :current_user, to: :scope` in my serializers. 

Issue #3: My GET requests would work with `$.ajax`, but didn't send back JSON if I used `$.fetch` or `$.get`

*TLDR: Include your credentials with fetch so the cookie sends.*

When I was first working on my GET requests through AJAX, I was trying to use the `fetch` method (thanks to an amazing lecture by Cernan that helped me get started). I thought I was setting it up right, but my response was always in HTML instead of JSON, and I couldn't get it to convert with `.serialize()`, `.json()`, or `.getJSON()`. After exhaustive Googling turned up no info I could understand, I switched my request to use the `.ajax` method, and it worked! I talked with Cernan about it, and he figured out that "when you send a fetch request, the cookie wasn't being sent in the fetch request, so when your before_action hook of require_login was returning false, causing a redirect to the root_path (check the rails server when trying it using the fetch request)". We were able to get it working with this code: 

```const getGames = (userId) => {
  fetch(`/users/${userId}/games.json`, {
    credentials: 'include'
  })
    .then(res => res.json())
    .then(games => console.log(games))```
		
Issue #4: Submitting a POST request from my AJAX rendered forms didn't work

This is an issue I didn't fix, but I would like to learn how to fix. After trying a million times, I scraped it because it was outside the scope of the project. I think my main issue was getting the authenticity token from my Rails app into my Javascript form template and having that pass correctly. I was pulling the csrf-token from the head of my HTML to populate the token value in the form, but it wasn't sending the correct information in the serialized form data - for some reason, it was only sending my hidden input fields. Whomp whomp. I currently have all of this code commented out so that I can come back to it. 

Next Improvement: Using Handlebar for my templates

I'm not counting this as an issue, because I have not tried to do this yet, but I think it would make my project cleaner. I currently have all my templates as Object Model prototype methods that format the HTML and add it to the DOM. I have a self imposed deadline though, so it might have to wait!
		
		


