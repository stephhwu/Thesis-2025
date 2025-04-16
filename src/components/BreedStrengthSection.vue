<template>
  <div class="breed-strength-section">
    <h1>Breed Strengths of the Most Popular Working Dogs</h1>  
    <div class="filters">
      <button 
        v-for="(data, breedId) in breedData" 
        :key="breedId"
        class="filter-button" 
        :class="{ active: currentBreed === breedId }"
        :data-breed="breedId"
        @click="updateBreed(breedId)"
      >
        {{ data.name }}
      </button>
    </div>
    
    <div class="breed-container">
      <div class="breed-info">
        <p class="optimal-role">Optimal Role: <span>{{ breedData[currentBreed].optimal }}</span></p>
        <div class="dog-image">illustration of dog</div>
        <h2 class="breed-name" ref="breedNameElement">{{ breedData[currentBreed].name }}</h2>
      </div>
      
      <div class="chart-container">
        <div id="radar-chart" ref="radarChart"></div>
      </div>
    </div>
      </div>
</template>

<script>
import { ref, onMounted, onBeforeUnmount, watch } from 'vue';
import * as d3 from 'd3';
import { gsap } from 'gsap';

export default {
  name: 'BreedStrengthSection',
  setup() {
    const radarChart = ref(null);
    const breedNameElement = ref(null);
    const currentBreed = ref('german-shepherd');
    let svgElement = null;

    // Breed data for the chart
    const breedData = {
      'german-shepherd': {
        name: 'German Shepherd',
        optimal: 'police/military',
        traits: [
          {axis: "Scent Detection", value: 8},
          {axis: "Protective Nature", value: 9},
          {axis: "Trainability", value: 10},
          {axis: "Intelligence", value: 9},
          {axis: "Energy Level", value: 8},
          {axis: "Adaptability", value: 8}
        ]
      },
      'border-collie': {
        name: 'Border Collie',
        optimal: 'herding',
        traits: [
          {axis: "Scent Detection", value: 6},
          {axis: "Protective Nature", value: 6},
          {axis: "Trainability", value: 10},
          {axis: "Intelligence", value: 10},
          {axis: "Energy Level", value: 9},
          {axis: "Adaptability", value: 7}
        ]
      },
      'labrador-retriever': {
        name: 'Labrador Retriever',
        optimal: 'service/assistance',
        traits: [
          {axis: "Scent Detection", value: 9},
          {axis: "Protective Nature", value: 5},
          {axis: "Trainability", value: 9},
          {axis: "Intelligence", value: 8},
          {axis: "Energy Level", value: 8},
          {axis: "Adaptability", value: 9}
        ]
      },
      'beagle': {
        name: 'Beagle',
        optimal: 'scent detection',
        traits: [
          {axis: "Scent Detection", value: 10},
          {axis: "Protective Nature", value: 4},
          {axis: "Trainability", value: 6},
          {axis: "Intelligence", value: 7},
          {axis: "Energy Level", value: 8},
          {axis: "Adaptability", value: 7}
        ]
      }
    };

    // Updated radar chart configuration
    const radarChartOptions = {
      w: 500 - 100,  // width minus margins
      h: 600 - 120,  // height minus margins (increased from 500)
      margin: {top: 100, right: 100, bottom: 100, left: 100}, // Increased margins
      maxValue: 10,
      levels: 5,
      roundStrokes: true,
      color: d3.scaleOrdinal().range(["#737373"]),
      transitionDuration: 800  // Animation duration in milliseconds
    };

    // Function to animate the breed name text
    function animateBreedName(newBreedName) {
      const breedNameEl = breedNameElement.value;
      if (!breedNameEl) return;
      
      // Create a GSAP timeline for the animation sequence
      const tl = gsap.timeline();
      
      // First, animate the current text out
      tl.to(breedNameEl, {
        opacity: 0,
        y: -50,
        duration: 0.3,
        ease: "power2.in",
        onComplete: () => {
          // Update the text content
          breedNameEl.textContent = newBreedName;
        }
      });
      
      // Then, animate the new text in
      tl.fromTo(breedNameEl, 
        { opacity: 0, y: 50 },
        { opacity: 1, y: 0, duration: 0.5, ease: "back.out(1.7)" }
      );
    }

    // Initialize the radar chart with improved positioning
    function initRadarChart() {
      if (!radarChart.value) return;
      
      const margin = radarChartOptions.margin,
            width = radarChartOptions.w + margin.left + margin.right,
            height = radarChartOptions.h + margin.top + margin.bottom;
      
      const svg = d3.select(radarChart.value).append("svg")
          .attr("width", width + 50)  // Add extra width
          .attr("height", height + 50)  // Add extra height
          .attr("class", "radar");
      
      svgElement = svg;
      
      // Append a g element
      const g = svg.append("g")
          .attr("transform", `translate(${(width/2)},${(height/2)})`);
      
      // Glow filter for the lines
      const filter = g.append('defs').append('filter').attr('id','glow'),
        feGaussianBlur = filter.append('feGaussianBlur').attr('stdDeviation','2.5').attr('result','coloredBlur'),
        feMerge = filter.append('feMerge'),
        feMergeNode_1 = feMerge.append('feMergeNode').attr('in','coloredBlur'),
        feMergeNode_2 = feMerge.append('feMergeNode').attr('in','SourceGraphic');
      
      // Draw the background circles
      const axisGrid = g.append("g").attr("class", "axisWrapper");
      
      const allAxis = breedData[currentBreed.value].traits.map(i => i.axis),
            total = allAxis.length,
            radius = Math.min(radarChartOptions.w/2, radarChartOptions.h/2),
            angleSlice = Math.PI * 2 / total;
      
      // Scale for the radius
      const rScale = d3.scaleLinear()
        .range([0, radius])
        .domain([0, radarChartOptions.maxValue]);
      
      // Draw background circles
      axisGrid.selectAll(".levels")
        .data(d3.range(1, (radarChartOptions.levels+1)).reverse())
        .enter()
        .append("circle")
        .attr("class", "gridCircle")
        .attr("r", d => radius / radarChartOptions.levels * d)
        .style("fill", "#CDCDCD")
        .style("stroke", "#CDCDCD")
        .style("fill-opacity", 0.1);
      
      // Text indicating at what % each level is
      axisGrid.selectAll(".axisLabel")
        .data(d3.range(1, (radarChartOptions.levels+1)).reverse())
        .enter().append("text")
        .attr("class", "axisLabel")
        .attr("x", 4)
        .attr("y", d => -d * radius / radarChartOptions.levels)
        .attr("dy", "0.4em")
        .style("font-size", "10px")
        .attr("fill", "#737373")
        .text(d => radarChartOptions.maxValue * d / radarChartOptions.levels);
      
      // Create the straight lines radiating outward from the center
      const axis = axisGrid.selectAll(".axis")
        .data(allAxis)
        .enter()
        .append("g")
        .attr("class", "axis");
      
      // Append the lines
      axis.append("line")
        .attr("x1", 0)
        .attr("y1", 0)
        .attr("x2", (d, i) => rScale(radarChartOptions.maxValue * 1.0) * Math.cos(angleSlice * i - Math.PI / 2))
        .attr("y2", (d, i) => rScale(radarChartOptions.maxValue * 1.) * Math.sin(angleSlice * i - Math.PI / 2))
        .attr("class", "line")
        .style("stroke", "white")
        .style("stroke-width", "2px");
      
      // Append the labels at each axis with improved positioning
      axis.append("text")
        .attr("class", "legend")
        .style("font-size", "14px")
        .attr("text-anchor", function(d, i) {
          // Custom text anchor based on position
          const angle = angleSlice * i - Math.PI / 2;
          // Bottom label needs "middle", left needs "end", right needs "start"
          if (Math.abs(Math.sin(angle)) > 0.9) { // Bottom or top
            return "middle";
          } else if (Math.cos(angle) < 0) { // Left side
            return "end";
          } else { // Right side
            return "start";
          }
        })
        .attr("dy", function(d, i) {
          // Adjust vertical position for bottom label
          const angle = angleSlice * i - Math.PI / 2;
          return Math.sin(angle) > 0.8 ? "1em" : "0.35em"; // Give more space at bottom
        })
        .attr("x", function(d, i) {
          const angle = angleSlice * i - Math.PI / 2;
          // Create specific spacing based on the axis position
          let distanceFactor;
          
          if (i === 0) { // Scent Detection (top)
            distanceFactor = 1.15;
          } else if (i === 1) { // Protective Nature (top-right)
            distanceFactor = 1.15;
          } else if (i === 2) { // Trainability (right)
            distanceFactor = 1.15;
          } else if (i === 3) { // Intelligence (bottom)
            distanceFactor = 1.05;
          } else if (i === 4) { // Energy Level (bottom-left)
            distanceFactor = 1.15;
          } else if (i === 5) { // Adaptability (left)
            distanceFactor = 1.15;
          }
          
          return rScale(radarChartOptions.maxValue * distanceFactor) * Math.cos(angle);
        })
        .attr("y", function(d, i) {
          const angle = angleSlice * i - Math.PI / 2;
          // Create specific spacing based on the axis position
          let distanceFactor;
          let yOffset = -10; // Move all labels up by 10px
          
          if (i === 0) { // Scent Detection (top)
            distanceFactor = 1.20;
          } else if (i === 1) { // Protective Nature (top-right)
            distanceFactor = 1.15;
          } else if (i === 2) { // Trainability (right)
            distanceFactor = 1.0;
          } else if (i === 3) { // Intelligence (bottom)
            distanceFactor = 1.00;
            yOffset = 0; // Don't shift the bottom label up
          } else if (i === 4) { // Energy Level (bottom-left)
            distanceFactor = 1.05;
          } else if (i === 5) { // Adaptability (left)
            distanceFactor = 1.15;
          }
          
          return rScale(radarChartOptions.maxValue * distanceFactor) * Math.sin(angle) + yOffset;
        })
        .text(d => d)
        .call(wrap, 60);
      
      // Create a wrapper for the blobs
      const blobWrapper = g.append("g")
          .attr("class", "radarWrapper");
      
      // The radial line function
      const radarLine = d3.lineRadial()
        .curve(d3.curveLinearClosed)
        .radius(d => rScale(d.value))
        .angle((d, i) => i * angleSlice);
      
      // Append the backgrounds
      blobWrapper.append("path")
        .attr("class", "radarArea")
        .attr("d", radarLine(breedData[currentBreed.value].traits))
        .style("fill", radarChartOptions.color(0))
        .style("fill-opacity", 0.2)
        .on('mouseover', function() {
          d3.select(this).transition().duration(200).style("fill-opacity", 0.7);
        })
        .on('mouseout', function() {
          d3.select(this).transition().duration(200).style("fill-opacity", 0.2);
        });
      
      // Create the outlines
      blobWrapper.append("path")
        .attr("class", "radarStroke")
        .attr("d", radarLine(breedData[currentBreed.value].traits))
        .style("stroke-width", "2px")
        .style("stroke", radarChartOptions.color(0))
        .style("fill", "none")
        .style("filter", "url(#glow)");
      
      // Append the circles
      blobWrapper.selectAll(".radarCircle")
        .data(breedData[currentBreed.value].traits)
        .enter()
        .append("circle")
        .attr("class", "radarCircle")
        .attr("r", 4)
        .attr("cx", (d, i) => rScale(d.value) * Math.cos(angleSlice * i - Math.PI / 2))
        .attr("cy", (d, i) => rScale(d.value) * Math.sin(angleSlice * i - Math.PI / 2))
        .style("fill", radarChartOptions.color(0))
        .style("fill-opacity", 0.8);
      
      // Set up the tooltip
      const tooltip = g.append("text")
        .attr("class", "tooltip")
        .style("opacity", 0);
      
      // Append invisible circles for tooltip
      blobWrapper.selectAll(".radarInvisibleCircle")
        .data(breedData[currentBreed.value].traits)
        .enter().append("circle")
        .attr("class", "radarInvisibleCircle")
        .attr("r", 6)
        .attr("cx", (d, i) => rScale(d.value) * Math.cos(angleSlice * i - Math.PI / 2))
        .attr("cy", (d, i) => rScale(d.value) * Math.sin(angleSlice * i - Math.PI / 2))
        .style("fill", "none")
        .style("pointer-events", "all")
        .on("mouseover", function(event, d) {
          const newX = parseFloat(d3.select(this).attr('cx')) - 10;
          const newY = parseFloat(d3.select(this).attr('cy')) - 10;
          
          tooltip
            .attr('x', newX)
            .attr('y', newY)
            .text(d.axis + ": " + d.value)
            .transition().duration(200)
            .style('opacity', 1);
        })
        .on("mouseout", function() {
          tooltip.transition().duration(200)
            .style("opacity", 0);
        });
    }

    // Function to update the radar chart with animation
    function updateRadarChart(breedId) {
      const breedInfo = breedData[breedId];
      
      if (!svgElement) return;
      
      const g = svgElement.select("g");
      if (!g) return;
      
      const margin = radarChartOptions.margin,
            radius = Math.min(radarChartOptions.w/2, radarChartOptions.h/2),
            angleSlice = Math.PI * 2 / breedInfo.traits.length;
      
      // Scale for the radius
      const rScale = d3.scaleLinear()
        .range([0, radius])
        .domain([0, radarChartOptions.maxValue]);
      
      // The radial line function
      const radarLine = d3.lineRadial()
        .curve(d3.curveLinearClosed)
        .radius(d => rScale(d.value))
        .angle((d, i) => i * angleSlice);
      
      // Select and update the radarArea
      const radarArea = g.select(".radarArea");
      if (radarArea) {
        radarArea.transition()
          .duration(radarChartOptions.transitionDuration)
          .attr("d", radarLine(breedInfo.traits));
      }
      
      // Select and update the radarStroke
      const radarStroke = g.select(".radarStroke");
      if (radarStroke) {
        radarStroke.transition()
          .duration(radarChartOptions.transitionDuration)
          .attr("d", radarLine(breedInfo.traits));
      }
      
      // Select and update the radarCircles
      const radarCircles = g.selectAll(".radarCircle");
      if (radarCircles) {
        radarCircles.data(breedInfo.traits)
          .transition()
          .duration(radarChartOptions.transitionDuration)
          .attr("cx", (d, i) => rScale(d.value) * Math.cos(angleSlice * i - Math.PI / 2))
          .attr("cy", (d, i) => rScale(d.value) * Math.sin(angleSlice * i - Math.PI / 2));
      }
      
      // Select and update the invisible circles for tooltips
      const invisibleCircles = g.selectAll(".radarInvisibleCircle");
      if (invisibleCircles) {
        invisibleCircles.data(breedInfo.traits)
          .transition()
          .duration(radarChartOptions.transitionDuration)
          .attr("cx", (d, i) => rScale(d.value) * Math.cos(angleSlice * i - Math.PI / 2))
          .attr("cy", (d, i) => rScale(d.value) * Math.sin(angleSlice * i - Math.PI / 2));
      }
    }

    // Helper function to wrap text
    function wrap(text, width) {
      text.each(function() {
        var text = d3.select(this),
            words = text.text().split(/\s+/).reverse(),
            word,
            line = [],
            lineNumber = 0,
            lineHeight = 1.4,
            y = text.attr("y"),
            x = text.attr("x"),
            dy = parseFloat(text.attr("dy")),
            tspan = text.text(null).append("tspan").attr("x", x).attr("y", y).attr("dy", dy + "em");
        
        while (word = words.pop()) {
          line.push(word);
          tspan.text(line.join(" "));
          if (tspan.node().getComputedTextLength() > width) {
            line.pop();
            tspan.text(line.join(" "));
            line = [word];
            tspan = text.append("tspan").attr("x", x).attr("y", y).attr("dy", ++lineNumber * lineHeight + dy + "em").text(word);
          }
        }
      });
    }

    // Function to update the current breed
    function updateBreed(breedId) {
      if (currentBreed.value === breedId) return;
      currentBreed.value = breedId;
      
      // Animate the breed name change
      animateBreedName(breedData[breedId].name);
      
      // Update the chart
      updateRadarChart(breedId);
    }

    onMounted(() => {
      // Initialize the chart with the first breed
      initRadarChart();
    });

    onBeforeUnmount(() => {
      // Clean up D3 elements to prevent memory leaks
      if (svgElement) {
        svgElement.remove();
        svgElement = null;
      }
    });

    return {
      radarChart,
      breedNameElement,
      breedData,
      currentBreed,
      updateBreed
    };
  }
};
</script>

<style scoped>
.breed-strength-section {
  max-width: 1200px;
  margin: 0 auto;
  padding: 40px 20px;
  font-family: -apple-system, BlinkMacSystemFont, 'Segoe UI', Roboto, Oxygen, Ubuntu, Cantarell, 'Open Sans', 'Helvetica Neue', sans-serif;
  height: 100vh; /* Ensure it takes full viewport height */
  display: flex;
  flex-direction: column;
  position: relative; /* Add this to create positioning context */
  isolation: isolate; /* Add this for stacking context isolation */
  scroll-snap-align: start; /* For section scrolling */
  scroll-snap-stop: always;
}

h1 {
  font-size: 36px;
  text-align: center;
  margin-bottom: 10px;
  font-family: "ivypresto-headline", serif;
  color: #FF0000;
  font-weight: normal;
}

.filters {
  display: flex;
  justify-content: center;
  gap: 10px;
  margin-bottom: 40px;
  margin-top: 40px;
}

.filter-button {
  background-color: #f0f0f0;
  border: none;
  border-radius: 20px;
  padding: 8px 20px;
  font-size: 16px;
  cursor: pointer;
  transition: background-color 0.3s;
}

.filter-button.active {
  background-color: #666;
  color: white;
}

/* Update breed-container to ensure it stays within its section */
.breed-container {
  display: flex;
  justify-content: space-between;
  margin-top: 30px;
  flex: 1;
  min-height: 0;
  position: relative; /* Add this */
  z-index: 1; /* Ensure proper stacking */
}

/* Update breed-info to ensure proper positioning */
.breed-info {
  flex: 1;
  padding-right: 60px;
  display: flex;
  flex-direction: column;
  position: relative; /* Add this */
}

/* Make sure the optimal-role is properly contained */
.optimal-role {
  font-size: 18px;
  color: #666;
  margin-bottom: 20px;
  position: relative; /* Add this */
  z-index: 1; /* Add this */
}

.breed-name {
  font-size: 60px;
  margin-bottom: 5px;
  margin-top: auto; /* Push to bottom of container */
  opacity: 1;
  position: relative;
  overflow: hidden;
  font-family: "ivypresto-headline", serif;
}

.dog-image {
  width: 400px;
  height: 400px;
  background-color: #eee;
  display: flex;
  justify-content: center;
  align-items: center;
  color: #666;
  text-align: center;
  margin-bottom: 20px;
}

.chart-container {
  flex: 1;
  position: relative;
}

.radar-tooltip {
  position: absolute;
  background-color: rgba(255, 255, 255, 0.9);
  border-radius: 5px;
  padding: 5px 10px;
  font-size: 14px;
  pointer-events: none;
  box-shadow: 0 2px 5px rgba(0, 0, 0, 0.1);
}

@media (max-width: 768px) {
  .breed-container {
    flex-direction: column;
  }
  
  .breed-info {
    padding-right: 0;
    margin-bottom: 40px;
  }
  
  .dog-image {
    width: 100%;
    height: 300px;
  }
}
</style>