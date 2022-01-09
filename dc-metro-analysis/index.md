# DC Metro Network Analysis


Network analysis is often applied to the social and online systems around us, for example, Facebook friend networks. A more concrete example of a physical network, however, is a metro system.

The DC metro is a straightforward example of a fully connected network system. The network can be described as nodes (stations) and edges (railway paths). With each additional connection to other stations, a station's importance to the network becomes more critical. 

Given this information, how can we understand the importance of each metro station? Metro nodes can be described with three main metrics: 

1. **Degree**: How many other stations a rider can reach from a given station

2. **Betweenness Centrality**: How much control a station has on the ability for a rider to reach other stations

3. **Closeness Centrality:**</b> How close a given station is to other stations

To understand the degree, betweenness, and closeness of a given station, we first need to represent the metro system in a dataset that the computer can understand. The best way to accomplish this is through an adjacency matrix of stations. Luckily, the Washington Metropolitan Area Transit Authority (WMATA) provides a series of [API products](https://developer.wmata.com) that make the fetching of station and line data easy. Code for how to build the adjacency matrix is in my [Github Repo](https://github.com/kevcraig/DC-Metro-Analysis/blob/main/Get%20Metro%20Data.py). I was surprised this data was not available anywhere I looked online, so I hope it is useful for anyone else who wants to explore metro analytics.
		
Once the network is expressed in an adjacency matrix, Python can help us calculate the network statistics discussed above. Finally, we can overlay those stats on top of network line and station shapefiles provided by WMATA and visualized via geopandas. Hover over points to view node statistics and press the magnifying glass to zoom.

<div align = "center">
	<iframe src="bokeh_wmata.html" frameborder="0" width="600" height="530"	></iframe>
</div>

Before I started this project, I assumed Metro Center would be the most *important* station based on my ridership patterns while living in DC. However, given the findings on this analysis, it looks like L'Enfant Plaza is the most important! L'Enfant has a degree of 5, betweenness of .571, and closeness of .144, not to mention an underground food court. Honorable mention goes to the Pentagon, the most important station outside of DC in terms of betweenness and closeness centrality.

Instead of using set shapefiles to draw the network, what if we let the computer decide how it should look? Force-directed algorithms allow us to visualize networks as nodes and edges that repel against each other based on a given charge. The D3 JavaScript library is a popular tool for building force-directed networks — I implemented D3 to create a free-flowing DC metro system below. Pull a node with your mouse and watch the network update positions.


<div style="width:960; margin:0 auto;">

<style>

	.links line {
	  stroke: #999;
	  stroke-opacity: 0.6;
	}
	
	.nodes circle {
	  stroke: #fff;
	  stroke-width: 1.5px;
	}
	
	text {
	  font-family: sans-serif;
	  font-size: 8px;
	}
	
	
	</style>
	
	
<script src="https://d3js.org/d3.v4.min.js"></script>
<svg width="960" height="800"></svg>
	
<script>


var svg = d3.select("svg"),
	width = +svg.attr("width"),
	height = +svg.attr("height");

var color = d3.scaleOrdinal(d3.schemeCategory20);

var simulation = d3.forceSimulation()
	.force("link", d3.forceLink().id(function(d) { return d.id; }))
	.force("charge", d3.forceManyBody().strength(-15))
	.force("center", d3.forceCenter(width / 2, height / 2));

d3.json("wmata.json", function(error, graph) {
  if (error) throw error;

  var link = svg.append("g")
	  .attr("class", "links")
	.selectAll("line")
	.data(graph.links)
	.enter().append("line")
	  .attr("stroke-width", function(d) { return Math.sqrt(d.degree); });

  var node = svg.append("g")
	  .attr("class", "nodes")
	.selectAll("g")
	.data(graph.nodes)
	.enter().append("g")
	
  var circles = node.append("circle")
	  .attr("r", function(d) { return d.degree * 3; })
	  .attr("fill", function(d) { return color(d.degree); })
	  .call(d3.drag()
		  .on("start", dragstarted)
		  .on("drag", dragged)
		  .on("end", dragended));

  var lables = node.append("text")
	  .text(function(d) {
		return d.id;
	  })
	  .attr('x', 6)
	  .attr('y', 3);

  node.append("title")
	  .text(function(d) { return d.id; });

  simulation
	  .nodes(graph.nodes)
	  .on("tick", ticked);

  simulation.force("link")
	  .links(graph.links);

  function ticked() {
	link
		.attr("x1", function(d) { return d.source.x; })
		.attr("y1", function(d) { return d.source.y; })
		.attr("x2", function(d) { return d.target.x; })
		.attr("y2", function(d) { return d.target.y; });

	node
		.attr("transform", function(d) {
		  return "translate(" + d.x + "," + d.y + ")";
		})
  }
});

function dragstarted(d) {
  if (!d3.event.active) simulation.alphaTarget(0.3).restart();
  d.fx = d.x;
  d.fy = d.y;
}

function dragged(d) {
  d.fx = d3.event.x;
  d.fy = d3.event.y;
}

function dragended(d) {
  if (!d3.event.active) simulation.alphaTarget(0);
  d.fx = null;
  d.fy = null;
}

</script>
</div>


Through network analysis, we can understand which stations are most important in terms of degree and centrality, then visualize the network with Python Bokeh or D3. A subsequent step would be to weight the nodes and edges based on how many people flow through a station each day and where they are traveling. This would give us a better idea of node centrality and could be interesting to view through time, especially during the workday. 

For anyone hoping to visualize networks in D3, I found that the library had a steep learning curve for someone without previous experience in JavaScript, but provided some really unique and customizable results once you got the hang of it. Using D3 with a physical network like the DC metro is kind of trivial since there is already a real physical representation, but would be very useful when visualizing social networks. A common example of D3 with social networks is [here](https://bl.ocks.org/mbostock/4062045) with Les Mis characters.

