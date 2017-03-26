---
layout: tut
title: "Mapping with D3.js - v2.0"
---

# Intro
[D3](http://d3js.org/) is a powerful data visualization library that helps connect your data to graphical elements, and then apply data-driven transformations to those elements. The basic idea is that when the data is bound to graphics, you can produce more portable graphics and much more dynamic visualization with less effort.

So *why use D3 for maps?* Maps are fundamentally graphical objects based on data, and D3 has built in support for map projections and transformations. D3 is actually the backend renderer for SVG images in the OpenStreetMap editor iD, so at least someone is using D3 for geospatial applications! You might also wonder why you would choose D3 over another library like [Leaflet.js](http://leafletjs.com/). That really depends on what you want to display, but there's no reason you can use both! Here is a great tutorial [example](https://bost.ocks.org/mike/leaflet/) using D3 to create dynamic overlays on a Leaflet map.

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
D3 has straightforward functions to grab data from a variety of sources including XMLHttpRequests, text files, JSON blobs, HTML document fragments, XML document fragments, comma-separated values (CSV) files, and tab-separated values (TSV) files. The data is converted to a D3 "selection", which is just an array. Part of the tremendous power of D3 is that it can select and join data elements quickly and efficiently.

## Driven

 At this point *Driven* might not make sense. If we stopped here, we could probably make a case to change *Driven* to *Defined*. Let's keep going.

**Driven** is actually one of the defining characteristics of D3: the document (graphics) are defined by the data. Consider a contrary example: what happens when you export a map from your desktop GIS software to do custom design work in a graphics editor? As soon as your vector data leaves the GIS, features lose the data that defines them. At best, the great attributes you honed are represented graphically and at worst are totally gone.

That type of thing doesn't happen in D3. Not only does your data define the elements in your SVG, the data is also bound (joined) to the elements in your document. A circle isn't just a circle element with an x,y and radius, it's also the data that originated the element in the first place. This characteristic of D3 allows data to drive your visualization, not only upon creation, but throughout its life cycle.

## Documents

At its core, D3 takes your information and transforms it into a visual output. That output is your document, and for all intents and purposes, your document is an [SVG](http://www.w3.org/TR/SVG/), but it can be any part of the HTML Document Object Model. Scalable Vector Graphics is a file format that encodes vector data for use in a wide array of applications, including your web browser. SVGs are used all over the place to display all kinds of data. If you've ever exported a map from [desktop GIS](http://www.qgis.org/en/site/) and styled it in a [graphics program](https://inkscape.org/en/), chances are your data was stored as SVG at some stage of the process.

SVGs are human readable, which works well for us because we aren't computers. This is an SVG in code:

```HTML
<svg width="720" height="120">
  <circle cx="40" cy="60" r="10"></circle>
  <circle cx="80" cy="60" r="10"></circle>
  <circle cx="120" cy="60" r="10"></circle>
</svg>
```

And this is the rendered version of that code:
<div>
<img src="https://ryshackleton.github.io/d3_maptime/img/threeLittleCircles.svg">
</div>

Click the image above or [this link](https://ryshackleton.github.io/d3_maptime/img/threeLittleCircles.svg) to see the same document embedded in a really simple web page. Right click one of the circles and select *Inspect element* from the drop down.

Each of those circles is an element in your SVG, which has a width and height. This is the type of document D3 writes to your web browser. You can tell it to add circles or move circles or remove circles. Checkout this [selection](http://bost.ocks.org/mike/circles/) tutorial for more on the subject. It's also worth noting that D3 has the ability to write and edit many types of shapes, not just circles.


# Tips

* The learning curve can be pretty steep. Stay positive
* Start simple, add complexity piece by piece
* Refer to documentation/tutorials
* **Cannibalize code** wherever/whenever you can. D3 has great examples and most the code is freely accessible. See something you like? Take a look at how it's done

# Tutorial Time!

With any luck, today we will produce a simplified version of [this map](https://ryshackleton.github.io/global_daily_earthquakes_d3/index.html), which displays the [USGS's GeoJSON feed of recent earthquakes around the world](https://earthquake.usgs.gov/earthquakes/feed/v1.0/geojson.php).  We'll do this in steps:

 1. Create a simple web page to hold our SVG graphic and the JaveScript code to create it.  The rest is all JavaScript...
 1. Create a "scatterplot" with some circles
 1. Explore D3's methods to scale data coordinates to graphics coordinates
 1. Explore D3's geographic projection methods scale geo-data coordinates to graphics coordinates
 1. Use D3 to read in GEOJSON data from the USGS daily earthquake feed
 1. Add country boundaries to the map
 1. Pretty it up!

By the time we finish this tutorial, we will have built our first (or nth) D3 web map! This will not be the prettiest map you've ever made, but hopefully once you've made it, you will have a launching pad to make an even better D3 map in the future.

## STEP 1: Review Web Pages

Before we get going, let's take a walk over to our [web map tutorial](http://maptimesea.github.io/2014/11/05/web-map-intro.html#let-s-get-started). While there, go through the steps to make an empty HTML file (through *Add a boilerplate to map.html*). 

At this stage, you should have a *map.html* file on your machine that you can open in your web browser. Give that a shot by dragging *map.html* into **Firefox**. This is what your file looks like:

<script src="https://gist.github.com/powersa/36be23aaa647c25c3236.js"></script>

Save your eyes. Feel free to delete lines 7-9:

```HTML
    body {
      background-color: green;
    }
```

### Include the D3.js library

Now you can insert D3 into your page. For now we'll use an externally hosted version of the library instead of copying it into a new file. Add the following after the `<head>` tag of `map.html`.

```HTML
  <!--   using version 4-->
  <script src="https://d3js.org/d3.v4.min.js"></script>
```

## STEP 2: Build the document

Before we add a map to our web page, we need to make a place to put it. The following work will take place between `<script>` tags.

### Define width and height of your graphic

```JavaScript
  var width = 700,
      height = 500;
```

### Create your SVG in the `<body>` element

D3 has really easy shorthand for electing and creating objects:
```JavaScript
  var svg = d3.select("body") // select the html element with <body>
              .append("svg"); // append an svg to that group
```

The chunk of code above inserts an SVG into the document, which it does in 2 steps

1. First, D3 asks the browser to return the html element with the tag: `<body>`,
1. Second, D3 takes that `<body>` element, and appends, or inserts an `<svg>` element into it, so that the body element now looks like this:

```html
 <body>
    <svg></svg>
 </body>
```

**Notice that we also assigned the svg element as a variable so we can do more things to it later.**

*Gaahhh!!!, why the empty space before .append(...)??*

This code is also an example of **method chaining**, which is common in some JavaScript libraries like jQuery.  Basically, select(), and append() return references to something (a d3 selection) that we operate on in the next link of the method chain. [See this page](http://alignedleft.com/tutorials/d3/chaining-methods) for a thorough discussion of method chaining in D3.

### Give your new `<svg>` some width and height
Right now the SVG has no height or width, so it won't be visible or even have a clickable area. To fix that, we'll use the svg variable we assigned above to add some attributes to the svg with the .attr() function, which adds attributes to objects in the web page.
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
Great, a blank image! Try adding this bit of code to show the boundary of the SVG. Notice that we used the .style() method instead of the .attr() method to apply styles, and the result is a style="stylename: style value;" on the element.

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

We'll start by defining the simplest array of data we can think of.
```JavaScript
  // a JavaScript array of data
  var myData = [ 5, 15, 30 ];
```

Then join that data array to some `<circle>`'s within the SVG
```JavaScript
  // the original svg starts out as an empty svg element (with its own attributes, but nothing inside it)
  var circles = svg.selectAll("circle") // select all circles within the SVG
     .data(myData) // JOIN the data to the circles,
                 // if the data is larger than the selection, make a circle "placeholder" for each data object
                // return a reference to the data object containing the placeholders
      .enter() // RETURN the "enter" selection, which is any newly created placeholders for objects
      .append("circle"); // on the enter selection, append a new circle object for each circle placeholder in the data
                        //   then return the selection
                        // NOW the svg contains 3 circles with no attributes
                        //  <svg "width"=700 "height"=500>
                        //      <circle></circle>
                        //      <circle></circle>
                        //      <circle></circle>
                        // </svg>
                        // ...and return the selection of circles
```

There's a lot going on in these 4 lines of code.  Let's go line by line:

1. svg.selectAll("circle") - This selects any circles already in the SVG.  More importantly, it returns a selection, *even if the selection is empty,* that we can operate on in the next line, and tells d3 that the "parent" of this object is the `<svg>` element.
<img src="https://ryshackleton.github.io/d3_maptime/img/01_selectAllCircles.svg">

1.    .data(myData) - This .data() method should really be called .join() because this is the magic that matches up new data to elements that are already in the SVG. In the background D3 creates 2 selections (arrays): an *enter selection* and an *exit selection*.  The *update selection* is what we're hoping to find, which is the join of the two datasets. The *enter selection* contains new data that is un-bound to any element. The *exit selection* contains all of the elements for which there is no new data.  In our case, there are no elements, so the *enter selection* becomes the *update selection*.
<img src="https://ryshackleton.github.io/d3_maptime/img/02_selectAllCirclesData.svg">

1.    .enter() - get that enter selection as an array
1.    .append("circle") - attach each of the data objects to a circle element within the svg.  This creates an update selection, and appends the data to circle object.  
<img src="https://ryshackleton.github.io/d3_maptime/img/03_selectAllCirclesDataEnterAppend.svg">
1. Then return the new updated circle selection as an array.
<img src="https://ryshackleton.github.io/d3_maptime/img/04_assignToCircles.svg">

<img src="https://ryshackleton.github.io/d3_maptime/img/enterUpdateExitII.svg">

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
