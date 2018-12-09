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

<div id="container"></div>
<div id="tooltip"></div>
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

<br />

# Predictions score
Our final predictions for 2018 data reached an accuracy of 89.89%.  
On a total of 435 districts, we predicted correctly 391 and mispredicted 44.  
Comparing predictions with actual results:  

<table>
  <thead>
    <tr>
      <th></th>
      <th>Predictions</th>
      <th>Actual results</th>
      <th>Before elections</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>N. Democrat districts</th>
      <td>186</td>
      <td>230</td>
      <td>194</td>
    </tr>
    <tr>
      <th>N. Democrat districts</th>
      <td>249</td>
      <td>205</td>
      <td>241</td>
    </tr>
  </tbody>
</table>

Looking the actual results we notice how all those 44 mispredictions went from democrats (actual result) to republicans (prediction), this means our model is slightly biased towards republicans. Looking at the situation before elections we notice how it has a tendency to confirm candidates which are running for re-election.  

The fact that our simple baseline model has an accuracy of 76.8% tells a lot about partisanship: several districts stick with the same party for long time.   

# Features

As we would expect, some of the electorate behaviors are seen in the data. The incumbent candidates have a great advantage compared to the opponents.  
Some of the interaction terms and engineered features got high scores in the models. For example the `Log10fundraising` and `ownPartisan`.  
On the other hand, it was surprising to see how some features which we would expect having great importance, were getting a low or not so high score in the models. For example the party of the president and the seats ratio of the candidate's party in the House of representative.  
In fact, during midterm elections the president usually loses seats in the House of Representative. We have to work to see how to translate in terms of statistical behavior what we know it is a natural electorate behavior.  
The data quality and collinearity of some features have certainly a big impact over those ones.

After having ran several different configurations, we saw how dropping some collinear features like `first_time_elected` or `count_victories` could bring to a more neutral model, but reduced the cross-validation scores overall.  

# Choosing the training data

We also noticed how selecting the training data influenced the model performances. Taking only midterm years as training set was improving the score of our boosting model against the others, while including also the presidential years was giving random forests as best, with also better stacking. So  

## Future improvements

### District redistribution

District redistribution reduces data quality dramatically. All districts coming after a redistribution have no historical data per se, prior the redistribution. We would propose to collect the data from the districts preceding the redistribution and apply them to create a state model. Using the state model and the others district data at every year we could impute the historical data for those districts.  

### Improvement by changing the modeling sequence

In our process to producing final predictions, we went through several optimization phases.  
In each phase we improved the current model, before passing to the next.  
Specifically, we refer to:  

- Compute cross-validation accuracy
- Improvement 1: mutual exclusive winning candidate per district
- Improvement 2: compute accuracy by district, instead than by candidate, including districts without predictions
- Stacking models predictions

We could potentially improve the score by changing this sequence and placing the stacking before the improvements 1 and 2.  
In fact, during improvement 1, when taking the candidate with the highest winning probability in each district, it happens to have exact the same probability between two candidates of opposite parties, so we can’t conclude which one is our winner and we set both to not-winning.  
After stacking, the winning probability of each candidate is the weighted average of winning probability from each model prediction, using the stacking linear model coefficients as weight.  
It would be, then, much less likely to have two candidates with the same winning probability after stacking. Therefore, the mutual exclusive selection would perform much more efficiently after stacking, instead than before. Due to lack of time within project deadline, we leave this test for further developments.  

### Outliers years

During EDA we didn’t analyze the data through the different years. This could be a way to drop some special conditions years which otherwise could mislead the predictions.