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
<svg width="400" height="120">
  <circle cx="40" cy="60" r="10"></circle>
  <circle cx="80" cy="60" r="10"></circle>
  <circle cx="120" cy="60" r="10"></circle>
</svg>
```

And this is the rendered version of that code:
<img src="https://ryshackleton.github.io/d3_maptime/img/threeLittleCircles.svg">

SVG's work similarly to html pages, where tags represent objects that can have *objects nested within them*: each circle is an element *nested within* the SVG. Each circle contains some coordinates of the object's center (cx, cy), and radius (r), so the SVG is just a set of instructions defining the geometry of each object, where to put each object, and how to style the objects.

It's also worth noting that D3 has the ability to write and edit [many types of shape elements](https://www.w3schools.com/graphics/svg_examples.asp) to SVG's, like rectangles (`<rect>`), not just circles.  Later we'll use a [`<path>` element](https://www.w3schools.com/graphics/svg_path.asp), which has a shorthand format for definining complex polygons like country boundaries.

# Tips

* The learning curve can be pretty steep. Stay positive
* Start simple, add complexity piece by piece
* Refer to [documentation](https://github.com/d3/d3/blob/master/API.md) / [tutorials](https://github.com/d3/d3/wiki/Tutorials)
* **Cannibalize code** wherever/whenever you can. D3 has [great examples](https://bl.ocks.org/) and most the code is freely accessible. See something you like? Take a look at how it's done, but definitely go line-by-line in the code to make sure you know what's happening!

# Tutorial Time!

With any luck, today we will produce a simplified version of [this map](https://ryshackleton.github.io/global_daily_earthquakes_d3/index.html), which displays the [USGS's GeoJSON feed of recent earthquakes around the world](https://earthquake.usgs.gov/earthquakes/feed/v1.0/geojson.php).  We'll do this in steps:

 1. Create a simple web page to hold our SVG graphic
 1. Learn to select SVG objects with D3
 1. Learn to create SVG objects with D3
 1. Explore D3's methods to scale data coordinates to graphics coordinates
 1. Explore D3's geographic projection methods scale geo-data coordinates to graphics coordinates
 1. Use D3 to read in GEOJSON data from the USGS daily earthquake feed
 1. Add country boundaries to the map
 1. Pretty it up!

By the time we finish this tutorial, we will have built our first (or nth) D3 web map! This will not be the prettiest map you've ever made, but hopefully once you've made it, you will have a launching pad to make an even better D3 map in the future.

## STEP 1: Create a simple web page to hold our SVG graphic

Below I've included the quick-and-dirtiest version EVER of how web pages work, along with some boilerplate HTML we can use to get started.  If you need more of a refresher on how to make a simple web page, have a look a our [web map tutorial](http://maptimesea.github.io/2014/11/05/web-map-intro.html#let-s-get-started).  

Like nouns, adjectives, and verbs, the web (in its simplest form) is made of HTML, CSS, and Javascript.

1. The HTML describes what's IN your webpage: the nouns.
1. The CSS describes what everything LOOKS like: the adjectives.
1. The JavaScript describes what your webpage DOES: the verbs.  

<div>
<img src="http://maptimesea.github.io/img/tut001-nounverbadj.svg">
</div>

**D3 is like a word processor that you use to write complete sentences: it helps you BUILD your webpage by constructing HTML, SVG, CSS, and even JavaScript elements dynamically from your data.**

### Copy this HTML code into a file called hello-d3.html in your text editor to get started

```HTML
<!doctype html>
<html lang="en">

<head>
  <meta charset="utf-8">
  
  <!--   using D3 version 4-->
  <script src="https://d3js.org/d3.v4.min.js"></script>
  
    <!--  could add CSS inside the <style> tags -->
    <style>
      
    </style>

</head>

<body>
  <svg width="400" height="120">
    <circle cx="40" cy="60" r="10"></circle>
    <circle cx="80" cy="60" r="10"></circle>
    <circle cx="120" cy="60" r="10"></circle>
  </svg>
  
  <script>
    /* Your JavaScript Here */
     
  </script>
</body>
</html>
```
Notice that we have already included the D3.js script (version 4) at the top of the page inside the `<head>` tag.

```HTML
  <!--   using version 4-->
  <script src="https://d3js.org/d3.v4.min.js"></script>
```

I have also included the SVG we discussed above inside the `<body>` tag, which we'll learn to manipulate with D3.  It should look like this.

```HTML
  <svg width="400" height="120">
    <circle cx="40" cy="60" r="10"></circle>
    <circle cx="80" cy="60" r="10"></circle>
    <circle cx="120" cy="60" r="10"></circle>
  </svg>
```
From here on out, most of what we'll be doing is writing JavaScript to select and add things to the web page.  In `hello-d3.html`, find the `<body>` tag, then find the `<script>` tag inside the `<body>`.  All of our code will go there.

```JavaScript 
    <script>
    	/* Your JavaScript Here */
    </script>
```
## STEP 2: Learn to select SVG objects with D3
### Your first D3 Selection

D3 has really easy shorthand for selecting objects in webpages.  First, we'll ask D3 to select the `<body>` tag, and then select the `<svg>` inside the body.  Again, writing your code between the `<script>` tags, add the following code.

```JavaScript
  var body = d3.select("body"); // select the html element with <body>
  
  var svg = body.select("svg"); // select the <svg> that lies within the <body>
```
You just did your first D3 Selection! Not very exciting, but this will provide us a little intro to how to debug your scripts in a web browser using the browser's Developer Tools.

##### Developer Tools for debugging
To see the contents of the variables ("body" and "svg")
1. go back to your web browser showing hello-d3.html
1. right click anywhere in the page
1. select "View Page Source" or "Inspect Element", which shows you the source code for the page
1. find the "Console" section or tab
1. type "svg" into the console to access your stored "svg" variable, which should reveal something like this:

```JavaScript
> dt {_groups: Array[1], _parents: Array[1]}
```
You can expand the dropdowns and see the contents of the svg object.  This is a handy way to get behind the scenes of your JavaScript code.  The console would also show you errors, which I'm sure you'll never have in your code.  You can also access the console directly from inside your code by adding things like:

```JavaScript
console.log("Here's my svg at step 1:");
console.log(svg);
```

### Select more than one thing at once...

How about selecting more than one thing, like, say, the circles in the SVG?  Easy.  Use **.selectAll()**

```JavaScript
  var circles = svg.selectAll("circles");
```
Here I'm looking *within the svg that I selected above* for all of the circle objects.  I could also write `d3.selectAll("circles");`, but that would give me any circles *anywhere in the web page*.  It's good to be specific so you don't end up selecting elements you didn't want to modify.

### 1st Challenge, 10 minutes: Change the Color and Size of the SVG elements
Head over to [this awesome tutorial](https://strongriley.github.io/d3/tutorial/circle.html), where the first section: **Selecting Elements** will show you how to give your circles some style.  Once you've found the code to change colors and sizes of circles, add the relevant code to your script and see if you cant make your circles have the following properties

 1. Radius: 20
 1. fill: darkred
 
Be sure to make use of the Developer Tools to find errors (in the Console).  You can even try to move your circles around in x/y space, but be careful how far you move them!!! (you can move them outside of the SVG and they'll be gone)
 
### 2nd Challenge, 10 minutes: Bind Some Data to your circles
Stick with the [same tutorial](https://strongriley.github.io/d3/tutorial/circle.html), but now move onto: **Binding Data**.  See if you can bind the following data to the `cy` attribute on each of your circles
```JavaScript
	var myData = [ 20, 60, 100 ];
```
### Advanced Challenge, if you have extra time: Add Transition Effects
Check out [the tutorial on Transitions](https://bost.ocks.org/mike/transition/), and see if you can transition from one state to another.  You probably want to wrap your code in a button so you can see the transition happen. To add a button, add the following snippet to your script (not the html), then add your transitioning code inside the `function myFunction()`

```JavaScript
    // use d3 to add a new button in the body
    d3.select("body")
      .append("button")
      .on("click",myFunction) // link the myFunction function to the button click
      .text("Run My Function"); // add some text to the button
    
    function myFunction() {
      // add your transitioning code here
    }
```
### Take 10 minutes to READ through the Creating Elements section
[Same tutorial](https://strongriley.github.io/d3/tutorial/circle.html), but now move onto: **Creating Elements**. We'll create some elements in the next section.  Pay special attention to the **.append() function**, the idea of the **enter selection** and to **method chaining** at the end.

## STEP 3: Start building our earthquake map
### Start with a clean slate: copy and paste the code below to a new file called myMap.html
```HTML
<!doctype html>
<html lang="en">

<head>
  <meta charset="utf-8">
  
  <!--   using D3 version 4-->
  <script src="https://d3js.org/d3.v4.min.js"></script>
  
    <!--  could add CSS inside the <style> tags -->
    <style>
      
    </style>

</head>

<body>  
  <script>
    /* Your JavaScript Here */
     
  </script>
</body>
</html>
```
Notice that there's NO SVG in the file this time: that's because we'll just have D3 create it for us!

### The Plan: Create a map with some circles representing earthquakes of varying size
Let's think about what we want our map to look like, then translate that into a general idea of what the SVG code will look like,
then figure out how to make D3 build it for us.

We know our SVG should go inside the `<body>`, it needs a a height and width, and that it should have some circles that have some attributes as we saw before.  So it should look something like this:

```HTML
<body>
   <svg width="some number" height="some number" style="some style with colors, borders, etc">
    <circle cx="some x" cy="some y" r="some radius" style="some style....;"></circle>
    <circle cx="some x" cy="some y" r="some radius" style="some style....;"></circle>
    <circle cx="some x" cy="some y" r="some radius" style="some style....;"></circle>
  </svg>
</body>
```
So our general plan could be to:
1. Create an SVG in the `<body>` with the .append() function
1. Give SVG a *width* and *height* attribute with .attr()
1. Give SVG some style with .style()
1. Get an array of earthquake data
	1. For each earthquake in the data array
		1. create a circle with the .append() function
		1. give circle a *cx* and *cy* attribute representing their coordinates (scaled to the SVG coordinates!)
		1. size the earthquake based on the earthquake's magnitude (set r to something with .attr())

Whoah.  That was a lot.  How about an easy decision? Let's start with deciding on a width and height for our graphic.
```JavaScript
	var width = 700;
      	var height = 500;
```
### Use D3 to magically create an SVG in the `<body>` element
To add things to the document, use d3's `.append()` function:
```JavaScript
	var svg = d3.select("body").append("svg"); // append an svg to the body, save it as a variable called svg
```
When we append() to the `<body>` element, D3 just adds a new `<svg>` below the script that we're actually writing.  Yeah, you heard that right: we're writing code to edit the document that we're writing....#mindblown.

Now the body element looks like this:

```html
 <body>
    <script> /* The script tag with the JavaScript code we're writing right now!! */ </script>
    <svg></svg>
 </body>
```
### Give your new `<svg>` some width, height, and style!
The SVG won't even be selectable until we assign the width and height attributes. Again, use the .attr() function, which adds attributes to objects in the web page or on the SVG.  Then we'll add some style with the .style() method, and the result is a style="stylename: style value;" on the element.
```JavaScript
  svg.attr("width", width)
                           // NOW svg looks like this in the document:
                           // <body>
                           //    <svg width="700"></svg>
                           // </body>
     .attr("height", height)
                           // NOW svg looks like this in the document:
                           // <body>
                           //    <svg width="600" height="500"></svg>
                           // </body>
     .style("border","3px solid black") // applies some CSS-like styles to the svg.
     .style("background-color","lightblue"); // applies some CSS-like styles to the svg.
                           // NOW svg looks like this in the document:
                           // <body>
                           //   <svg width="700" height="500" style="border: 3px solid black; background-color: lightblue;">
                           // </body>
                           // </body>
 ```
You may notice that I have started **chaining** these methods together.  I can do that because the .attr() and .style() method return a selection containing the svg we're working on.  You'll see **method chaining** *a lot* in D3.

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
