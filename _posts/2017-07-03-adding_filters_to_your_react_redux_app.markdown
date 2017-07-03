---
layout: post
title:  "Adding Filters to your React/Redux app"
date:   2017-07-03 00:52:12 -0400
---


When I started my React/Redux project, I knew I wanted to add filters (what good restaurant related app doesn't have filters?!?). I assumed this would be easy enough since they are ubiquitous on the web, but I was at a loss about how to implement this. Thanks to Luke, I got some direction, which I will share with you here. I want to preface this by saying, I do not know if this is the BEST way to do this. I haven't gotten feedback on it yet, but I know that it works. I will update if this is a dumb way to do it :)

**GOAL**: Filter restaurant list by price, location, and takeout option

**DATA**: The data I am using is coming from the Yelp Fusion API. It is first fetched by my Rails API backend to get only local data. Pertinent data points from the Yelp information is saved in my Rails database to share in this application. These are the database fields I'm using:
* Price: 4 string options ["$", "$$", "$$$", "$$$$"]
* Zip Code: 2 string options ["29401", "29403"]. The zip codes separate the city at 1 main road. Users select the side of town they want to stay on by selecting "North of Calhoun" or "South of Calhoun", and it filters by zip code.
* Takeout: Boolean (True for Yes and False for No or No Data)

# STEP 1: Create Visibility Filter Reducer

I created a reducer with an initial state:

```
let initialState = {
    priceFilter: [],
    locationFilter: [],
    takeoutFilter: false,
  }```
	
This allows you to have multiple selections for your price filter and location filter, and a simple true or false option for Takeout, which only has the one option. Then, write reducers to ADD and REMOVE items from each of these filter states. For example:
	
```
case 'ADD_PRICE_FILTER':
      return Object.assign({}, state, { priceFilter: [...state.priceFilter, action.priceType], locationFilter: [...state.locationFilter], takeoutFilter: state.takeoutFilter})

case 'REMOVE_PRICE_FILTER':
      const newPriceFilter = state.priceFilter.filter((item) => item !== action.priceType)
      return { priceFilter: newPriceFilter, locationFilter: [...state.locationFilter], takeoutFilter: state.takeoutFilter}
```
			
### STEP 2: Create Actions

You also need to create your actions for each of these reducers. For the price and zip code filters, we are going to send the target information to our reducer action, so you will write then like this:

```
export const addPriceFilter = (priceType) => {
  return {
    type: 'ADD_PRICE_FILTER', priceType
  }
}

export const removePriceFilter = (priceType) => {
  return {
    type: 'REMOVE_PRICE_FILTER', priceType
  }
}
```

The Takeout filter is simpler, so we do not need to pass any payload to the reducer. This state just toggles between true and false.

### STEP 3: Create Filter Component

This component is going to connect to the Redux store, so be sure to `import { connect } from 'react-redux'`. Also, you need to import all of your actions. We also need to use mapStateToProps to map our visibility filter state to this component's props. 

I used checkboxes in the browser so users can select multiple items in the filters. This is also great because you can easily use `e.target.clicked` in your click handlers to determine if you want to dispatch the ADD or REMOVE actions. I set up three different click handler functions in the component - one for each type of filter. Here is an example:

```
handlePriceFilter(e) {
    let priceType = e.target.value
    if (e.target.checked) {
      this.props.dispatch(addPriceFilter(priceType));
    } else {
      this.props.dispatch(removePriceFilter(priceType));
    }
  }
	```
	
This is dispatched when checkbox gets clicked because I have added an onClick listener to each input field:
	
	```
	<input type="checkbox" value="$" onClick={(e) => this.handlePriceFilter(e)}/>$<br />
	```
	
In the browser, the filter will look like this when you have a few items clicked:
	
![](http://i.imgur.com/wf6ngvQ.png?1)

This will update your state like this:

 ![](http://i.imgur.com/S3ZUDgI.png?1)

### STEP 4: Write the function to change your Visible Restaurants

This is the convoluted part. I have another Component for my visible restaurants that uses all of the restaurants in the restaurantData state and the filters in the visibilityFilter states to return only the restaurants the user wants to see. Outside of the actual React Component class, I defined a function `getVisibleRestaurants` that takes in arguments of the restaurantData and filters. In this filter, I used a long list of if statements to go through all of the filters. The first statement is the situation where no filters are selected, and all of the restaurants are returned. 
```
if (filter.priceFilter.length === 0 && filter.locationFilter.length === 0 && filter.takeoutFilter === false) {
    return restaurants
```

The next scenario has no price or location filters, but does include the takeout filter.

```
} else if (filter.priceFilter.length === 0 && filter.locationFilter.length === 0 && filter.takeoutFilter === true) {
    return restaurants.filter((r) => r.takeout === true)
```

Next, you could have a selection of every type of filter.

```
} else if (filter.priceFilter.length !== 0 && filter.locationFilter.length !== 0 && filter.takeoutFilter === true) {
    var filteredRestaurants1 = []
    restaurants.forEach(r => {
      if (filter.priceFilter.indexOf(r.price) !== -1 && filter.locationFilter.indexOf(r.zip_code) !== -1) {
        filteredRestaurants1.push(r)
      }
    })
    return filteredRestaurants1.filter((r) => r.takeout === true)
```

Keep going to create options for:
* price & location filters, no takeout filter
* price and takeout filters only
* price filter only
* location and takeout filters only
* location filter only

STEP 5: Render correct restaurants

In the render section of this Component, we call this long function passing in your states:

```
var restaurants = getVisibleRestaurants(this.props.restaurantData, this.props.visibilityFilter)
```

Lastly, you pass this variable to your Restaurant List Component. This Component will go through each item in the restaurants variable and render a Restaurant component for each restaurant. 


THAT'S IT! Again, I think there should be another, probably better way to write the `getVisibleRestaurants` function. The length of the function would grow exponentially as you added more filters. This was my first foray into filters, and I hope to get better next time!


