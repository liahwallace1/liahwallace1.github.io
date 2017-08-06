---
layout: post
title:  "Planning a React/Redux App"
date:   2017-08-06 22:11:54 +0000
---


So now that I've graduated the Flatiron School, I have a lot going on with job searching, being a Tech Coach at Flatiron, and doing some side work for friends, but I have also started working on a new React/Redux app to keep my skills sharp, and I'm going to blog my process. It's called *Is it Wet?* 💦 (hehe), and the goal is to let a user search for a location and see the current forcast and the past few days of weather to determine if conditions are wet. I like to climb, and since I live in Charleston, SC, the closest outdoor climbing is about 4 hours away. It is SUPER annoying when I drive all the way to North Carolina based on a clear weather forecast for the day only to find the rock is too moist to climb on. 

Most weather sites will only show you the future weather forecast, but I often need to see a few days of past weather data to determine if conditions are good. Unfortunately, I'm no meteorologist, so I don't have a super mathy weather algorithm to determine the conditions, but I do know of an API I can use to get past data that might be helpful. 

This will be a single page application built totally with React and Redux. I will be pulling from the Google Maps Geocoding/Javascript APIs to allow users to search for or click to select the exact location they are interested in. Google geocoding will allow me to get latitude and longitude for the location, and I will plug that data into the Dark Sky API to get our weather data. Users will be able to click thorugh views for a couple days of past weather and the current forecast. 

I know I want to use Redux in this app, so I first need to do some planning. (In truth, I already started building, but now I need to think about my State before I screw myself up). I will also outline my components. 

## State:

```
var initialState = {
   locationCoods: {lat: 32.765914, lng: -79.899101},  #if location services are allowed in the user's browser, this will be reset to the user's current location in componentWillMount(). Will update when searchTerm is updated
	
	searchTerm: "", #this is the state of the mapSearch Component
	
	fetchingData: false, #this will be true while fetching weather data
	
	weatherDataForecast: {}, #this will be populated by Dark Sky Forecast Requesst
	
	weatherDataPast: {day1: {}, day2: {}}, #this will be populated by 2 Dark Sky Time Machine Requests
	
  forecastKey: null, #this is the selected weather view
}```

## Components:

### App.js
* Sets location to currentLocation using navigator.geolocation if Location Services allowed in browser
* Holds my app header with the title
* Holds my connected map container
* Holds my NavBar and Forcast view when we have weather data

### MapContainer.js
* Sends API key to GoogleAPI so I can view my map
* Holds my Map component
* Holds my SearchForm component

### Map.js
* Gets map props from google to show the map
* Has onClick function to set new location with a click
* Shows a marker on the location (from this.state.location)

### SearchForm.js
* Allows user to type in search parameters to find coordinates
* Has arrow button to reset map to current location (if allowed)
* Has onChange function to update searchTerm state
* Uses Google Geocoding API show search results in dropdown as searchTerm updates (How?)
* On submit or dropdown click, updates locations

### Navbar.js
* Navigation buttons for each weather view

### WeatherView.js
* Shows the selected weather view as selected in the Navbar. 
* Defaults to current forecast
* Has options for 2 days ago, yesterday, current, future forecast
* Shows temperature, humidity, dew point, precipitation, cloud cover - all things to help you determine wetness

### Prediction.js
* Shows a simple prediction above the weather view - probably wet or probably dry based on current and past weather




I think this is it! We will see how this plays out as I'm creating, and I will write status updates as I go. Keep your eye out for technical blogs solving problems I research through the process. 
