---
layout: tut
title: "Mapping with D3.js - v2.0"
---

# Intro
[D3](http://d3js.org/) is a powerful data visualization library written by [Mike Bostock](https://bost.ocks.org/mike/) that helps connect your data to graphical elements, and then apply data-driven transformations to those elements. The basic idea is that when the data is bound to graphics, you can produce more portable graphics and much more dynamic visualization with less effort.

So *why use D3 for maps?* Maps are fundamentally graphical objects based on data, and D3 has built in support for map projections and transformations. D3 is actually the backend renderer for SVG images in the [OpenStreetMap editor iD](https://github.com/openstreetmap/iD), so that's a pretty good endorsement for D3 mapping! You might also wonder why you would choose D3 over another library like [Leaflet.js](http://leafletjs.com/). That really depends on what you want to display, but there's no reason you can use both! Here is a great tutorial [example](https://bost.ocks.org/mike/leaflet/) using D3 to create dynamic overlays on a Leaflet map.

### What do I need for this tutorial?

To successfully follow this tutorial you need a text editor (such as Notepad++, Brackets, or Sublime Text). Go ahead and use your personal favorite, or if you don't have one installed,
download [Sublime Text](http://www.sublimetext.com/) or [Brackets](http://brackets.io).

Also, to complete this tutorial without running a local web server (not what we're here to learn), you'll need to open your web map in Firefox or Chrome. Download [Firefox](https://www.mozilla.org/en-US/firefox/new/).

### What can I create with D3?

Check out these links for some examples of D3 visualizations:

[The full gallery of D3 visualizations](https://github.com/d3/d3/wiki/Gallery)

[Geographic Projections: animated](https://bl.ocks.org/mbostock/3711652)

[Wealth of Nations](https://bost.ocks.org/mike/nations/)

[Two Centuries of US Immigration](http://metrocosm.com/us-immigration-history-map.html)

[Ummm, really?](https://bl.ocks.org/mbostock/1136236)

[Ryan's Global Obesity Data Viz](https://d3-obesity-viz.herokuapp.com/)

[Ryan's Earthquake Visualizations (D3 & Leaflet)](https://ryshackleton.github.io/global_daily_earthquakes_d3_leaflet/)

# D3 - Data Driven Documents
D3 stands for **Data Driven Documents**. We will unpack this title in three parts.

## Data
D3 has straightforward functions to grab data from a variety of sources including XMLHttpRequests, text files, JSON blobs, HTML document fragments, XML document fragments, comma-separated values (CSV) files, and tab-separated values (TSV) files. Part of the tremendous power of D3 is that it can take data from a variety of sources, merge different data sources, and then join data elements to the visual elements that represent the data.

## Driven

**Driven** is actually one of the defining characteristics of D3: the graphical elements are defined by the data.  In other words, each circle, line, or polygon also contains the data they are defined by. A desktop GIS software works in the same way while you're working on your map, but when you export the map, the vector-based features lose the data that defines them. If you export a raster image those attributes are completely converted to color values and the data is detached completely.

That type of thing doesn't happen in D3. Not only does your data define the elements in your graphic, the data is also bound (joined) to the elements in your document. A circle isn't just a circle element with an x,y and radius, it's also the data that originated the element in the first place. This characteristic of D3 allows data to drive your visualization, not only upon creation, but throughout its life cycle.

## Documents

At its core, D3 takes your information and transforms it into a visual output. That output is your document, and for all intents and purposes, your document is a [Scalable Vector Graphic, or SVG](http://www.w3.org/TR/SVG/). SVG is a file format that encodes vector data for use in a wide array of applications. SVGs are used all over the place to display all kinds of data. If you've ever exported a map from [desktop GIS](http://www.qgis.org/en/site/) and styled it in a [graphics program](https://inkscape.org/en/), chances are your data was stored as SVG at some stage of the process.

SVGs are human readable, which works well for us because we aren't computers. This is an SVG in code:

```HTML
<svg width="720" height="120">
  <circle cx="40" cy="60" r="10"></circle>
  <circle cx="80" cy="60" r="10"></circle>
  <circle cx="120" cy="60" r="10"></circle>
</svg>
```

And this is the rendered version of that code:
<img src="https://ryshackleton.github.io/d3_maptime/img/threeLittleCircles.svg">

SVG's work similarly to html pages, where tags represent objects that can have *objects nested within them*: each circle is an element *nested within* the SVG. Each circle contains some coordinates of the object's center (cx, cy), and radius (r), so the SVG is just a set of instructions defining the geometry of each object, where to put each object, and how to style the objects.

It's also worth noting that D3 has the ability to write and edit [many types of shape elements](https://www.w3schools.com/graphics/svg_examples.asp) to SVG's, like rectangles (`<rect>`), not just circles.  Later we'll use a [`<path>` element](https://www.w3schools.com/graphics/svg_path.asp), which has a shorthand format for definining complex polygons like country boundaries.

We can also **group** items with the [g element](https://www.w3.org/TR/SVG/struct.html#Groups), which comes in handy when we start to build more complex SVG's with lots of different items that we want to keep organized.  The SVG below is the same SVG we saw above, but we've added a `<rect>` element grouped inside a `<g>` element, and the three `<circle>`'s are grouped inside a different `<g>` element.

```HTML
<svg width="720" height="120">
  <g>
    <rect width="300" height="100" style="fill: blue;"></rect>
  </g>
  <g>
    <circle cx="40" cy="60" r="10"></circle>
    <circle cx="80" cy="60" r="10"></circle>
    <circle cx="120" cy="60" r="10"></circle>
  </g>
</svg>
```
...and the rendered version:

<img src=https://ryshackleton.github.io/d3_maptime/img/threeLittleCirclesGroupedRect.svg></img>

If you want to see the svg code in your browser window, open [this link](https://ryshackleton.github.io/d3_maptime/img/threeLittleCirclesGroupedRect.svg), then *right click* in the SVG and select *Inspect element* from the drop down.

# Tips

* The learning curve can be pretty steep. Stay positive
* Start simple, add complexity piece by piece
* Refer to documentation/tutorials
* **Cannibalize code** wherever/whenever you can. D3 has [great examples](https://bl.ocks.org/) and most the code is freely accessible. See something you like? Take a look at how it's done, but definitely go line-by-line in the code to make sure you know what's happening!

# Tutorial Time!

With any luck, today we will produce a simplified version of [this map](https://ryshackleton.github.io/global_daily_earthquakes_d3/index.html), which displays the [USGS's GeoJSON feed of recent earthquakes around the world](https://earthquake.usgs.gov/earthquakes/feed/v1.0/geojson.php).  We'll do this in steps:

 1. Create a simple web page to hold our SVG graphic
 1. Write some JavaScript calling D3 to create a "scatterplot" with some circles
 1. Explore D3's methods to scale data coordinates to graphics coordinates
 1. Explore D3's geographic projection methods scale geo-data coordinates to graphics coordinates
 1. Use D3 to read in GEOJSON data from the USGS daily earthquake feed
 1. Add country boundaries to the map
 1. Pretty it up!

By the time we finish this tutorial, we will have built our first (or nth) D3 web map! This will not be the prettiest map you've ever made, but hopefully once you've made it, you will have a launching pad to make an even better D3 map in the future.

## STEP 1: Create a simple web page template

Below I've included the quick-and-dirtiest version EVER of how web pages work, along with some boilerplate HTML we can use to get started.  If you need more of a refresher on how to make a simple web page, have a look a our [web map tutorial](http://maptimesea.github.io/2014/11/05/web-map-intro.html#let-s-get-started).  

Like nouns, adjectives, and verbs, the web (in its simplest form) is made of HTML, CSS, and Javascript.

1. The HTML describes what's IN your webpage: the nouns.
1. The CSS describes what everything LOOKS like: the adjectives.
1. The JavaScript describes what your webpage DOES: the verbs.  

<div>
<img src="http://maptimesea.github.io/img/tut001-nounverbadj.svg">
</div>

**D3 is like a word processor that you use to write complete sentences: it helps you BUILD your webpage by constructing HTML, SVG, CSS, and even JavaScript elements dynamically from your data.**

### Copy this HTML to a file called hello-d3.html to get started

Here's a basic boilerplate for a webpage that contains each of the 3 "ingredients."

<script src="https://gist.github.com/Ryshackleton/e3182682c731b9e54028061ee30af6d9.js"></script>

Notice that we have already included the D3.js script (version 4) at the top of the page inside the `<head>` tag.

```HTML
  <!--   using version 4-->
  <script src="https://d3js.org/d3.v4.min.js"></script>
```

## STEP 2: Use D3 to build your first SVG!

From here on out, most of what we'll be doing is writing JavaScript to build our SVG map.  In the `hello-d3.html`, find the `<body>` tag, then find the `<script>` tag inside the `<body>`.  All of our code will go there.

### Define width and height of your graphic

Add the new code, and all subsequent code, between the `<script>/* Your JavaScript Here */</script>` tags in the `<body>`.

```JavaScript
  <body>
    <script>
    	/* Your JavaScript Here */
	var width = 700,
      	    height = 500;
	/* keep adding new code below this line... */
	
    </script>
  </body>
```

### Use D3 to magically create your SVG in the `<body>` element

D3 has really easy shorthand for electing and creating objects.  First, we'll ask D3 to select the `<body>` tag, and then append a new SVG inside the body.

```JavaScript
  var svg = d3.select("body") // select the html element with <body>
              .append("svg"); // append an svg to that group
```
Now the body element looks like this:

```html
 <body>
    <script> /* The script tag with the JavaScript code we're writing right now!! */ </script>
    <svg></svg>
 </body>
```

When we append() to the `<body>` element, we just add the new `<svg>` below the script that we're actually writing.  Yeah, you heard that right: we're writing code to edit the document that we're writing....#mindblown.

You might also be wondering what's going on with that empty space before .append(...). That's because D3 uses **method chaining**, which is common in some JavaScript libraries like jQuery.  To read more about method chaining [see this page](http://alignedleft.com/tutorials/d3/chaining-methods).

### Give your new `<svg>` some width and height
The SVG won't even be selectable until we assign the width and height variables. To fix that, we add some attributes to the svg with D3's .attr() function, which adds attributes to objects in the web page or on the SVG.
```JavaScript
  svg.attr("width", width)
                           // NOW svg looks like this in the document:
                           // <body>
                           //    <svg width="700"></svg>
                           // </body>
     .attr("height", height);
                           // NOW svg looks like this in the document:
                           // <body>
                           //    <svg width="600" height="500"></svg>
                           // </body>
 ```

### Give SVG some style
Great, we made a blank image! Try adding this bit of code to show the boundary of the SVG. Notice that we used the .style() method instead of the .attr() method to apply styles, and the result is a style="stylename: style value;" on the element.

 ```JavaScript
  svg.style("border","3px solid black"); // applies some CSS-like styles to the svg.
  svg.style("background-color","lightblue"); // applies some CSS-like styles to the svg.
                           // NOW svg looks like this in the document:
                           // <body>
                           //   <svg width="700" height="500" style="border: 3px solid black; background-color: lightblue;">
                           // </body>
                           // </body>
 ```

If you refresh the html page, you should see your SVG in with a blue background, outlined with a solid black line. In your browser, right click in the box and select *Inspect Element*, and you'll see the stylish svg element in your page, hopefully within the `<body></body>` tag.

We'll use SVG styling more below to style individual elements. [Here's the full documentation](https://www.w3.org/TR/SVG/styling.html) for styling SVG elements for reference.

### DATA JOINING: Put some `<circle>`'s in your SVG!

We'll start by defining a simple array that we'll bind, or join, to the circles.

```JavaScript
  // a JavaScript array of data
  var myData = [ 5, 15, 30 ];
```
#### D3 Selections
D3 mimics data arrays with the concept of a *selection*, which is an array of graphical **elements** like circles, rectangles, html tags, lines, or anything else we can select in the HTML.  So remember:

	DATA: [ data, data, data ], where data can be numbers or objects containing other data
	SELECTION: [ element, element, element ], where elements are circles, rectangles, etc.

The code below is the standard way that we match up the data in the DATA arrays to the elements in the SELECTION arrays.  We'll join our simple array of numbers to a *selection* of `<circle>`'s within the SVG.

###### Heads up!
The `// comments` I have included are certainly [tl;dr](https://en.wikipedia.org/wiki/TL;DR) for most people, but I've included them for reference.  Selections and data binding are the most conceptually difficult parts of D3, so if this seems confusing, don't worry. It takes a while to wrap your head around. For several more thorough explanations of the data binding method, start with Mike Bostock's [Three Little Circles Tutorial](https://strongriley.github.io/d3/tutorial/circle.html), which has some nice animations of how selections work.  You can also check out [How Selections Work](https://bost.ocks.org/mike/selection/) and [Thinking with Joins](https://bost.ocks.org/mike/join/), also by Mike. [This Presentation](https://bost.ocks.org/mike/d3/workshop/#0) also offers a good overview.

OK, here we go!
```JavaScript
  var circles = svg.selectAll("circle") // select all circles within the SVG -> returns a SELECTION [] (empty in our case)
     .data(myData) // JOIN the DATA [5,15,30] to the empty SELECTION [] in 3 steps:
                  //  1) For any elements in SELECTION[] that have corresponding data in DATA[]: overwrite their data values
		  //     This set becomes the "update selection" (update selection is empty in our case)
		  //  2) For any data in DATA[] that don't have corresponding elements in SELECTION [],
		  //      create a SELECTION[] of new "dummy" elements and attach a data object to each element
		 //	  This is called the "enter selection" -> SELECTION[ element->data, element->data, element->data ]
		 //   3) For elements in SELECTION[] that don't have corresponding data in DATA[],
		 //       into an "exit selection"
		 //   4) return the update, enter, and exit selections { update, enter, exit }
      .enter() // get the "enter selection" from the returned object
      .append("circle"); // on the "enter selection", append a new circle object for each "dummy" element we created 
                        //   then return the selection, which is now: SELECTION[ circle->data, circle->data, circle->data ]
                        // NOW the svg contains 3 circles with no attributes
                        //  <svg "width"=700 "height"=500>
                        //      <circle></circle>
                        //      <circle></circle>
                        //      <circle></circle>
                        // </svg>
```
There's a lot going on in these 4 lines of code, but here's what's happening in a more visual manner:

We select an empty array of **elements**, or circles, using svg.selectAll("circles").

<div>
<img src="https://ryshackleton.github.io/d3_maptime/img/d3.data.png">
</div>

Then we JOIN our data to the empty array, creating any necessary circles using .data(myData).enter().append("circle").

<div>
<img src="https://ryshackleton.github.io/d3_maptime/img/d3.data.circles.png">
</div>

##### D3's Selection/Array syntax
You may notice we don't use for or forEach loops to do work on D3 selections.  That's because the .append(something) methods are defined on the selection object. So .append(something) appends an object of type "something" to EACH of the elements in the selection. 

At the end of all of this, even though you can't see it in the SVG, the data is still attached as a variable called `__data__` within the `<circle>` elements.  This is how D3 can match up the incoming data to the existing circle elements.



### Define your projection

```JS
	var projection = d3.geo.mercator()
        .scale(96) // scales your map
		.translate([width / 2, height / 2]); // centers in SVG
```

When you define a projection, you tell D3 how to transform your data from spherical to Cartesian coordinates. Take a look at the [projection docs](https://github.com/mbostock/d3/wiki/Geo-Projections) to get a sense of how you can project your data with D3. 

### Translate to screen coordinates

```JS
	var path = d3.geo.path()
        .projection(projection);
```

When D3 renders your SVG, it has to translate geo coordinates to pixel coordinates on your screen. This functionality is called a *path generator*. In this block, we store our path generator in the variable *path* so we can access it through out our code.

At this point, all you have is an empty SVG on your page. Now that our **document** is ready, we need **data**.

## STEP 3: Grab map data

The data we'll use for this map is stored in the GeoJSON format. We won't address specifics here, but if you want to learn more about the spatial format GeoJSON, take a look at [GeoJSON & Git](http://maptimesea.github.io/2014/12/03/git-geojson.html).

Your data is waiting for you [here](https://gist.github.com/powersa/b59b786da0ca44080c35). 

![Download Gist](/img/tut003_download_gist.png)

Download the data and unpack it! If you don't have software to unzip the data, you can also copy the raw contents to a file on your computer and save as *us.json*. Move *us.json* to the same location as *map.html*.

## STEP 4: Load the data

When you complete this step, you will have a map. Below your path generator, insert the following:

```JS
	d3.json("us.json", function(json) { // loads JSON file
	  svg.selectAll("path") // selects path elements, will make them if they don't exist
	   .data(json.features) // iterates over geo feature
	   .enter() // adds feature if it doesn't exist as an element
	   .append("path") // defines element as a path
	   .attr("d", path) // path generator translates geo data to SVG
	});
```

This chunk of code goes through *us.json* and adds each feature to the SVG on your page. This is a great time to *Inspect Element* on your web page or dig into the GeoJSON. As you can see, your SVG has a "path" element (shape) for each feature in your GeoJSON. With a little extra digging, you will find that feature attributes in the source data are preserved in your visualization. See how data drives this document?

**Congratulations!** You built a web map in D3.js!

## STEP 5: Challenge

1. **Center the projection**. Hint: look at the [docs](https://github.com/mbostock/d3/wiki/Geo-Projections#center). What is a natural place to add this information?
2. **Change the scale**. Hint: where did we set scale again?
3. **Change the projection**. Dump that mercator! Hint: search "mercator()" and try [something else](https://github.com/mbostock/d3/wiki/Geo-Projections)
4. **Find the data**. Use your JS console to access a element data. Hint: it involves a [selection](https://github.com/mbostock/d3/wiki/Selections)

## Fin.

Hope that was helpful! Please fill out our survey when you are done even if you couldn't attend the meeting. We want to make sure the MaptimeSEA tutorials are teaching what **you** want to learn. [bit.ly/maptimesea_survey](http://bit.ly/maptimesea_survey)
