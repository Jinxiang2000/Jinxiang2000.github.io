---
title: "My Skills"
date: 2024-01-15
draft: false
description: "Technical skills and expertise of Jinxiang Ma"
showDate: false
showAuthor: false
showReadingTime: false
showEdit: false
heroStyle: "background"
---

{{< lead >}}
Explore my technical expertise through this interactive skill tree. Click on the nodes to expand and discover my capabilities across different domains.
{{< /lead >}}

## Interactive Skill Tree

<div id="skill-tree"></div>

<style>
.node {
  cursor: pointer;
}

.node circle {
  fill: #fff;
  stroke: rgb(53, 204, 156);
  stroke-width: 1.5px;
}

.node text {
  font: 12px sans-serif;
}

.link {
  fill: none;
  stroke: #ccc;
  stroke-width: 1.5px;
}

#skill-tree {
  width: 100%;
  height: 600px;
  margin: 20px 0;
  border: 1px solid #ddd;
  border-radius: 8px;
}
</style>

<script src="//d3js.org/d3.v3.min.js"></script>
<script>
var margin = {top: 20, right: 120, bottom: 20, left: 120},
    width = 960 - margin.right - margin.left,
    height = 600 - margin.top - margin.bottom;

var i = 0,
    duration = 750,
    root;

var tree = d3.layout.tree()
    .size([height, width]);

var diagonal = d3.svg.diagonal()
    .projection(function(d) { return [d.y, d.x]; });

var svg = d3.select("#skill-tree").append("svg")
    .attr("width", width + margin.right + margin.left)
    .attr("height", height + margin.top + margin.bottom)
  .append("g")
    .attr("transform", "translate(" + margin.left + "," + margin.top + ")");

d3.json("/flare.json", function(error, flare) {
  if (error) throw error;

  root = flare;
  root.x0 = height / 2;
  root.y0 = 0;

  function collapse(d) {
    if (d.children) {
      d._children = d.children;
      d._children.forEach(collapse);
      d.children = null;
    }
  }

  root.children.forEach(collapse);
  update(root);
});

function update(source) {
  var nodes = tree.nodes(root).reverse(),
      links = tree.links(nodes);

  nodes.forEach(function(d) { d.y = d.depth * 180; });

  var node = svg.selectAll("g.node")
      .data(nodes, function(d) { return d.id || (d.id = ++i); });

  var nodeEnter = node.enter().append("g")
      .attr("class", "node")
      .attr("transform", function(d) { return "translate(" + source.y0 + "," + source.x0 + ")"; })
      .on("click", click);

  nodeEnter.append("circle")
      .attr("r", 1e-6)
      .style("fill", function(d) { return d._children ? "lightsteelblue" : "#fff"; });

  nodeEnter.append("text")
      .attr("x", function(d) { return d.children || d._children ? -10 : 10; })
      .attr("dy", ".35em")
      .attr("text-anchor", function(d) { return d.children || d._children ? "end" : "start"; })
      .text(function(d) { return d.name; })
      .style("fill-opacity", 1e-6);

  var nodeUpdate = node.transition()
      .duration(duration)
      .attr("transform", function(d) { return "translate(" + d.y + "," + d.x + ")"; });

  nodeUpdate.select("circle")
      .attr("r", 4.5)
      .style("fill", function(d) { return d._children ? "lightsteelblue" : "#fff"; });

  nodeUpdate.select("text")
      .style("fill-opacity", 1);

  var nodeExit = node.exit().transition()
      .duration(duration)
      .attr("transform", function(d) { return "translate(" + source.y + "," + source.x + ")"; })
      .remove();

  nodeExit.select("circle")
      .attr("r", 1e-6);

  nodeExit.select("text")
      .style("fill-opacity", 1e-6);

  var link = svg.selectAll("path.link")
      .data(links, function(d) { return d.target.id; });

  link.enter().insert("path", "g")
      .attr("class", "link")
      .attr("d", function(d) {
        var o = {x: source.x0, y: source.y0};
        return diagonal({source: o, target: o});
      });

  link.transition()
      .duration(duration)
      .attr("d", diagonal);

  link.exit().transition()
      .duration(duration)
      .attr("d", function(d) {
        var o = {x: source.x, y: source.y};
        return diagonal({source: o, target: o});
      })
      .remove();

  nodes.forEach(function(d) {
    d.x0 = d.x;
    d.y0 = d.y;
  });
}

function click(d) {
  if (d.children) {
    d._children = d.children;
    d.children = null;
  } else {
    d.children = d._children;
    d._children = null;
  }
  update(d);
}
</script>

## Core Competencies

### Data Science & Analytics
- **Data Cleaning & Manipulation**: Expert in data preprocessing and transformation
- **Data Visualization**: Creating compelling visual narratives with data
- **Machine Learning**: Building predictive models and implementing ML algorithms
- **Natural Language Processing**: Text analysis and sentiment analysis
- **Bayesian Data Analysis**: Statistical modeling and inference

### Mathematics & Statistics
- **Linear Algebra**: Matrix operations and vector spaces
- **Real Analysis**: Mathematical foundations and proofs
- **Probability Theory**: Statistical distributions and hypothesis testing
- **Time Series Analysis**: Forecasting and temporal data analysis
- **Operations Research**: Optimization and decision science

### Programming Languages
- **Python**: Data science, machine learning, and automation
- **R**: Statistical computing and data analysis
- **SQL**: Database querying and data manipulation
- **C++**: Systems programming and algorithm implementation
- **JavaScript**: Web development and interactive visualizations

### Tools & Technologies
- **Jupyter Notebook**: Interactive data science environment
- **RStudio**: R development environment
- **MySQL**: Database management
- **Microsoft Azure**: Cloud computing and ML services
- **Google BigQuery**: Large-scale data analytics

{{< button href="/projects" target="_self" >}}
See My Projects
{{< /button >}} 