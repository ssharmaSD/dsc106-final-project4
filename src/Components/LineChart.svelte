<script>
  import { onMount } from 'svelte';
  import { scaleBand, scaleLinear } from 'd3-scale';
  import { select } from 'd3-selection';
  import { csv } from 'd3-fetch';
  import { axisBottom, axisLeft } from 'd3-axis';
  import * as d3 from 'd3';

  let width = 550;
  let height = 550;

  const margin = { top: 10, right: 30, bottom: 90, left: 40 };
  const innerWidth = width - margin.left - margin.right;
  const innerHeight = height - margin.top - margin.bottom;

  let selectedCountry = 'USA';
  let countries = [];

  function updateHeading(country) {
    select('#chartHeading').text(`Alcohol Consumption in ${country}`);
  }

  onMount(() => {
    const svg = select('#my_dataviz')
      .append('svg')
      .attr('width', width)
      .attr('height', height)
      .append('g')
      .attr('transform', `translate(${margin.left},${margin.top})`);

    const tooltip = select('body')
      .append('div')
      .attr('class', 'tooltip')
      .style('opacity', 0)
      .style('position', 'absolute')
      .style('background-color', 'lightgray')
      .style('border', '1px solid gray')
      .style('padding', '5px')
      .style('font-size', '16px')
      .style('pointer-events', 'none')
      .style('max-width', '200px')  // Set max-width for tooltip
      .style('word-wrap', 'break-word');  // Enable text wrapping

    csv("/drinks.csv").then(data => {
      data.forEach(d => {
        d.beer_servings = +d.beer_servings;
        d.spirit_servings = +d.spirit_servings;
        d.wine_servings = +d.wine_servings;
        d.total_litres_of_pure_alcohol = +d.total_litres_of_pure_alcohol;
      });

      countries = data.map(d => d.country);
      countries.sort(); // Optional: sort the country names

      updateChart(selectedCountry);
      updateHeading(selectedCountry);

      const dropdown = select('#countryDropdown');

      dropdown.selectAll('option')
        .data(countries)
        .enter()
        .append('option')
        .text(d => d)
        .attr('value', d => d);

      // Set the default selected option to 'USA'
      dropdown.property('value', selectedCountry);

      dropdown.on('change', function() {
        selectedCountry = this.value;
        updateChart(selectedCountry);
        updateHeading(selectedCountry);
      });

      function updateChart(country) {
        const countryData = data.find(d => d.country === country);
        if (!countryData) {
          console.error('Country not found in data.');
          return;
        }

        const barData = [
          { label: 'Beer Servings', value: countryData.beer_servings, color: '#a35108' },
          { label: 'Spirit Servings', value: countryData.spirit_servings, color: '#a8e1e3' },
          { label: 'Wine Servings', value: countryData.wine_servings, color: '#8a0101' },
        ];

        const x = scaleBand()
          .range([0, innerWidth])
          .domain(barData.map(d => d.label))
          .padding(0.2);

        svg.selectAll('*').remove();

        svg.append('g')
          .attr('transform', `translate(0,${innerHeight})`)
          .call(axisBottom(x))
          .selectAll('text')
          .attr('transform', 'translate(-10,0)rotate(-45)')
          .style('text-anchor', 'end')
          .style('font-size', '14px')
          .style('font-weight', 'bold');

        const y = scaleLinear()
          .domain([0, d3.max(barData, d => d.value)])
          .nice()
          .range([innerHeight, 0]);

        svg.append('g')
          .call(axisLeft(y))
          .selectAll('text')
          .style('font-weight', 'bold');

        svg.selectAll('.bar')
          .data(barData)
          .enter()
          .append('rect')
          .attr('class', 'bar')
          .attr('x', d => x(d.label))
          .attr('width', x.bandwidth())
          .attr('fill', d => d.color)
          .attr('y', y(0))
          .attr('height', d => innerHeight - y(0))
          .on('mouseover', function(event, d) {
            select(this).style('fill', 'orange'); // Change color on hover
            tooltip.transition().duration(200).style('opacity', 0.9); // Show tooltip
            tooltip.html(`Annually in ${selectedCountry} the average amount of ${d.label.toLowerCase()} is ${d.value} servings.`)
              .style('left', `${event.pageX + 5}px`) // Position tooltip
              .style('top', `${event.pageY - 28}px`);
          })
          .on('mousemove', function(event) {
            tooltip.style('left', `${event.pageX + 5}px`) // Update tooltip position
              .style('top', `${event.pageY - 28}px`);
          })
          .on('mouseout', function(event, d) {
            select(this).style('fill', d.color); // Revert color
            tooltip.transition().duration(500).style('opacity', 0); // Hide tooltip
          })
          .transition()
          .duration(800)
          .attr('y', d => y(d.value))
          .attr('height', d => innerHeight - y(d.value))
          .delay((d, i) => i * 100);
      }
    }).catch(error => {
      console.error('Error loading the CSV file:', error);
    });
  });
</script>

<style>
  .bar:hover {
    fill: orange;
  }

  .axis text {
    font-size: 12px;
  }

  .axis path,
  .axis line {
    fill: none;
    stroke: black;
    shape-rendering: crispEdges;
  }

  /* Tooltip Styling */
  .tooltip {
    position: absolute;
    background-color: lightgray;
    border: 1px solid gray;
    padding: 5px;
    font-size: 16px;
    pointer-events: none;
    opacity: 0;
    max-width: 200px;  /* Set max-width for tooltip */
    word-wrap: break-word;  /* Enable text wrapping */
  }
</style>

<h1 class="body-header">Alcohol Type Consumption by Country</h1>

<p class="body-text">
  With an understanding of how alcohol consumption began in ancient times, let us explore how it looks in modern day.
</p>

<p class="body-text">
  <strong>Now it's your turn to explore!</strong>
</p>

<p class="body-text">
  <strong>Choose a country from the dropdown/type it in the box**</strong> 
  to generate a bar chart describing different consumptions rates of different 
  alcohol types of your chosen country.
</p>

<p class="body-text">
  The original data set can be found from this 
  <a href="https://github.com/fivethirtyeight/data/tree/master/alcohol-consumption">fivethirtyeight</a> link. 
</p>

<p class="body-text">
  <strong>**</strong>For this prototype our bar chart only shows information for one hard-coded
  country and we hope to improve the interaction of this in the final model.
</p>

<p class="body-text">
  <strong>Note:</strong> We have had a lot of trouble trying to get this bar chart to center,
  any guidance that could be provided on this would be greatly appreciated. Thanks!
</p>

<div>
  <label for="countryDropdown">Select a country: </label>
  <select id="countryDropdown"></select>
</div>
<h2 id="chartHeading">Alcohol Consumption in USA</h2>
<div id="my_dataviz"></div>
