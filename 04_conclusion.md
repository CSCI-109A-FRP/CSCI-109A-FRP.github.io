---
layout: page
title: Conclusion
permalink: /conclusion
---

<style>
#container {
  text-align: center;
}
#tooltip {
  position: absolute;
  top: 0;
  left: 0;
  z-index: 10;
  margin: 0;
  padding: 10px;
  width: 200px;
  height: 70px;
  color: #000;
  font-family: sans-serif;
  font-size: 0.9em;
  font-weight: bold;
  text-align: center;
  background-color: #fff;
  opacity: 0;
  pointer-events: none;
  border-radius:5px;
  transition: .2s;
}
</style>

<div id="container"/>
<div id="tooltip"/>
<script src="https://d3js.org/d3.v4.min.js"></script>
<script src="https://d3js.org/topojson.v1.min.js"></script>
<script src="https://cdnjs.cloudflare.com/ajax/libs/d3-composite-projections/1.0.1/d3-composite-projections.min.js"></script>
<script>
var width = 960,
  height = 500;

var projection = d3.geoAlbersUsaTerritories();
var path = d3.geoPath()
  .projection(projection);

var svg = d3.select("#container").append("svg")
  .attr("width", width)
  .attr("height", height);

  var t = d3.transition();
d3.json("us_house_results_map.json", function(error, us) {
var us = topojson.feature(us, us.objects.us_congressional_districts);
svg.selectAll(".region")
    .data(us.features)
    .enter()
    .append("path")
    .attr("class", "region")
    .attr("d", path)
    .style("fill", function(d){
      console.log('Alpha:', d.properties.alpha);
      if(d.properties.PARTY_AFF=="Democrat") {
        return `rgba(69, 170, 242, ${d.properties.alpha})`;
      } else if (d.properties.PARTY_AFF=="Republican") {
        return `rgba(255, 47, 47, ${d.properties.alpha})`;
      } else {
        return "#efefef";
      }
    })
    .style("stroke", "#000")
    .style("stroke-width", "0.3px")
    .on("mouseover", function(d){
      //Show the tooltip
      var x = d3.event.pageX;
      var y = d3.event.pageY - 40;

      d3.select("#tooltip")
        .style("left", x + "px")
        .style("top", y + "px")
        .style("opacity", 1)
        .html( d.properties.STATE + " dist: " + d.properties.CONG_DIST + "<br/>" +d.properties.PARTY_AFF + "<br/>Chance of winning:" + (d.properties.alpha !== 'NaN' ? d.properties.alpha.toFixed(2) : 'NaN') );
      })
      .on("mouseout", function(){
        //Hide the tooltip
        d3.select("#tooltip")
          .style("opacity", 0);
      });;

svg
  .append("path")
    .style("fill","none")
    .style("stroke","#000")
    .style("stroke-dasharray","5,5")
    .attr("d", projection.getCompositionBorders());

});

</script>
