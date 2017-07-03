---
layout: post
title:  "Adding a Google Map to your React/Redux App"
date:   2017-07-03 06:47:34 +0000
---


I added a Google Map to my React/Redux final project, and I ran into some issues doing this for the first time. It isn't hard, but there are some key things you need to know! I will go through how I added and did some basic styling on my map. 

I took most of my instructions from [this blog post](https://www.fullstackreact.com/articles/how-to-write-a-google-maps-react-component/) from Full Stack React, but I felt like some items were missing.

**STEP 1: Install google-maps-react modules**

```
npm install google-maps-react --save
```

I used parts of this module to get all of the scripts necessary to get the map to work. We'll get back to that shortly!

**STEP 2: Get a Google Maps API Key**

You have to get a Google Maps API from their developers page. You can do that [HERE](https://developers.google.com/maps/documentation/javascript/). Then, click on the "Get a Key" link. When you set up your project they will ask a few questions. You want to use the Google Maps Javascript API call it from the Web Browser (Javascript).

**STEP 3: Create a Container Component**

This component will be responsible for loading the Google Api. The key steps here are:

* Import the `GoogleApiWrapper` from the `google-maps-react` module in this component. 
* Set a `const style` variable with the size or your container.
* Return a \<div\> that will hold your Map Component that we will create soon. Send a prop to your Map that says `google={this.props.google}` along with anything else you might need. 
* When you export your Container, you need to include your API key, API version, with your container. 

Below is my code for this container. I am also passing restaurant data to my map, because I want to use the latitude and longitude of my restaurant to plot on the map. You would replace this with your data if needed. Since I'm using `connect()` here, I'm including this when I export, but if you don't need that, you can export like this:

`export default GoogleApiWrapper({`
  `apiKey: __GAPI_KEY__, , version: '3.27'`
`})(Container)`

![](http://i.imgur.com/ROJ4Npv.png?1)

**STEP 4: Create a Map Component**

Now that we have our Container, we can create the Map Component. We don't have to import anything special here. In this component, you need to:
* render a `<div ref='map' style={style}></div>` for your map
* Create a `componentDidMount()` that creates the map and other features
* add style variable with width and height

First, let set up our basic component that renders the map. See the image below. We are using the ref attribute here because it allows you to select a DOM Node to put the map into. In my code, I'm setting up the width to be responsive (at the end of this post), but you can set this to whatever you need. You just have to have it!

![](http://i.imgur.com/ZY3nsyX.png?1)

In order to make sure that our API data is available in the props passed from the container, we now want to make a `ComponentDidMount()` function. That means that we wait until the component mounts and receives the google prop from the Container component to create the map and other features. 

Here we are going to create our new map, and I also created a marker for the map. I'm using my restaurant location data here to get my latitude and longitude, but you can use whatever position you need. Just make sure it is an object set up like this: `{lat: YOUR_LAT, lng: YOUR_LNG}`.

The basic structure of creating the map is:

`new this.props.google.maps.Map(this.refs.map, {`
  `center: MAP_POSITION_OBJECT,`
	`zoom: 14`
`})`

If you want to create a marker on your map, you will need to input your map data, so it would be helpful to create a map variable when you are creating your new map. The basic structure of creating a Marker is:

`new this.props.google.maps.Marker({`
  `map: MAP_VARIABLE,`
	`position: MARKER_POSITION_OBJECT`
`})`

Here is my function for componentDidMount(). Add this within your React Component and before you call render() with your map \<div\>. 

![](http://i.imgur.com/n54ISJh.png?1)


NOW, you should have a functioning map!! I wanted my map to stay in it's `<div>` boundaries and respond to a window resize, so I added some more.

**STEP 5: Formatting and resizing map with resize**

The first thing I wanted to do, was keep the map in it's `<div>` borders. I kind of hacked this my making the width feature of the map style variable equal to the window's width minus the margin width. For me, it was 90px. 

`width: ${window.innerWidth - 90}px`

This got the map within it's boundary, but every time I changed the width of my screen, the map size did not change. This was a little harder to implement. To do this, I added a state for map-width and initially set it equal to zero. I put this in the reducer that was handling other state objects for the map page, but you could keep it by itself too. I added the reducer:

`case 'UPDATE_MAP_WIDTH':`
     `return {map_width: window.innerWidth - 90}`
		 
I also added the action for this reducer to the Actions file. 

`export const updateMapWidth = () => {`
  `return {`
    `type: 'UPDATE_MAP_WIDTH'`
  `};`
`}`

In the main component that is holding the map feature, I added a `resize` event listener that would dispatch the UPDATE_MAP_WIDTH reducer. I also added code to remove the event listener when the component was preparing to unmount. If you look at my GitHub repo, this code is in the Recommendation.js file, but it should go in the main page holding the map. 

`componentDidMount() {`
  `window.addEventListener('resize', () => this.props.dispatch(updateMapWidth()));`
`}`

`componentWillUnmount() {`
   `window.removeEventListener('resize', () => this.props.dispatch(updateMapWidth()));`
 `}`
	
	Lastly, you just need to call `mapStateToProps()` in your Map Component to map the map_width state you created to the component. Then, in the width field, you replace `window.innerWidth - 90` with `this.props.map_width` (edit to accomodate your projects state setup).
	
	Sweet! Now, when the window is resized, your updateMapWidth() action will be called, which will change the state of map_width to your window size minus the margin size you chose. This state change will trigger the map component to re-render at the appropriate size. 


That's it! You can see all of my code at my [GitHub Repo](https://github.com/liahwallace1/chs-lunch-finder-client) for this project!
