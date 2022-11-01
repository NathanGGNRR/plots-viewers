# **WORK ITEM MENTION:**
#97

# To create a map diagram with d3js, you need to call the following script : 
`<script src="https://d3js.org/d3.v5.js"></script>`

Everything is done in a single html div with which the script will work.

## First, in your script, you have to :

### Define dimensions of the diagram :
`const width = 550, height = 550;`

### Next you need to create a path
`const path = d3.geoPath();`

It will allow to work with geometric data

### Make the projection of the map :
```
    const projection = d3.geoConicConformal()
    .center([2.454071, 46.279229])
    .scale(2600)
    .translate([width / 2, height / 2]);
```

Here, projection is  centered on France.
### And assign it to the path :
`path.projection(projection);`

### Now, create the svg corresponding to the div :
```
    const svg = d3.select('#map').append("svg")
        .attr("id", "svg")
        .attr("width", width)
        .attr("height", height);

    const deps = svg.append("g");
```

### Then initialize the tooltip
```
    var div = d3.select("body").append("div")   
      .attr("class", "tooltip")               
      .style("opacity", 0);
```

This tooltip wil show infromations corresponding to selected department.

### And now we create the method which will parse the JSON to add the tooltip

```
d3.json('file.json').then(function(geojson) {			
  deps.selectAll("path")
    .data(geojson.features)
    .enter()
    .append("path")
    .attr('class', 'department')
    .attr("d", path)
    .on("mouseover", function(d) {
      div.transition()        
        .duration(200)
        .style("opacity", .9);      
      div.html("Département : " + d.properties.CODE_DEPT + " - " + d.properties.NOM_DEPT + "<br/>"
         +  "Région : " + d.properties.NOM_REGION + "<br/>"
         +  "Superficie viticole : " + d.properties.SUPERFICIE + " m²<br/>"
         +  "Nombre de parcelles viticoles : " + d.properties.NB_PLOTS)
         .style("left", (d3.event.pageX + 30) + "px")     
         .style("top", (d3.event.pageY - 30) + "px")
     })
     .on("mouseout", function(d) {
       div.style("opacity", 0);
       div.html("")
         .style("left", "-500px")
         .style("top", "-500px");
     });
        
});
```

In this example, we used a JSON file so we have `d3.json()`
So in a first time we create the content of the div to which we will add attributes with specific values.
Then we add informations corresponding to the tooltip, we have a `mouseover` and a `mouseout`.
When we are on a department, the tooltip is shown with informations entered, those informations are those contained in `div.html()`.

This div is styled with the following css :

```
div.tooltip {
  position: absolute;
  text-align: center;
  color: white;
  width: 275px;
  height: 60px;
  padding: 2px;
  font: 12px sans-serif;
  background: grey;
  border: 0px;
  border-radius: 8px;
  pointer-events: none;
}
```

# To create an evolution diagram with d3js, you will recall the same script than in the map :
`<script src="https://d3js.org/d3.v5.js"></script>`

## Like before, you have to initialize :
### Dimensions of the diagram
It will depends on the wanted diagram.
### Creation of the svg linked to the div
This is necessary because without div, data is not printed.

## Next, you have to make the method which will parse data :

```
d3.csv("data.csv",

    function(d){
      return { year : d.year, total : d.total }
    },

    // Now I can use this dataset:
    function(data) {

      // X axis
      var x = d3.scaleLinear()
          .domain([2009, d3.max(data, function(d) { return +d.year })])
          .range([0, width]);
      svg.append("g")
          .attr("transform", "translate(0," + height + ")")
          .call(d3.axisBottom(x));

      var div = d3.select("body").append("div")	
      .attr("class", "tooltip")				
      .style("opacity", 

      // Y axis
      var y = d3.scaleLinear()
        .domain([0, d3.max(data, function(d) { return +d.total; })])
        .range([ height, 0 ]);
      svg.append("g")
        .call(d3.axisLeft(y));

      svg.append("path")
        .datum(data)
        .attr("fill", "none")
        .attr("stroke", "steelblue")
        .attr("stroke-width", 1.5)
        .attr("d", d3.line()
          .x(function(d) { return x(d.year) })
          .y(function(d) { return y(d.total) })
      )

  });
```

Here, we work with a CSV file so we have `d3.csv()`.
First, format the data.
Next, you scale and draw X axis and Y axis.
Finally, you add new elements.