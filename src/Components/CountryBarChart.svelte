<script>
  import { onMount } from 'svelte';
  import { scaleBand, scaleLinear } from 'd3-scale';
  import { select } from 'd3-selection';
  import { axisBottom, axisLeft } from 'd3-axis';
  import * as d3 from 'd3';

  let width = 550;
  let height = 550;

  const margin = { top: 10, right: 30, bottom: 90, left: 40 };
  const innerWidth = width - margin.left - margin.right;
  const innerHeight = height - margin.top - margin.bottom;

  let selectedCountry = 'USA';
  let countries = [];

  // Define button texts and corresponding countries
  const buttonRecords = [
    { text: 'Country with the most consumption', country: 'Andorra' },
    { text: 'Country with the least consumption', country: 'Afghanistan' },
    { text: 'Country that drinks the most beer', country: 'Namibia' }
  ];

  function updateHeading(country) {
    select('#chartHeading1').text(`Alcohol Consumption in ${country}`);
  }

  function updateChart(country) {
    fetch("/drinks.csv")
      .then(response => response.text())
      .then(text => {
        const data = d3.csvParse(text);
        data.forEach(d => {
          d.beer_servings = +d.beer_servings;
          d.spirit_servings = +d.spirit_servings;
          d.wine_servings = +d.wine_servings;
          d.total_litres_of_pure_alcohol = +d.total_litres_of_pure_alcohol;
        });

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

        const svg = select('#my_dataviz svg g');

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

        // Add horizontal grid lines
        svg.append('g')
          .attr('class', 'grid')
          .call(d3.axisLeft(y)
            .tickSize(-innerWidth)
            .tickFormat(''));

        const bars = svg.selectAll('.bar')
          .data(barData)
          .enter()
          .append('g');

        bars.append('rect')
          .attr('class', 'bar')
          .attr('x', d => x(d.label))
          .attr('width', x.bandwidth())
          .attr('fill', d => d.color)
          .attr('y', y(0))
          .attr('height', d => innerHeight - y(0))
          .transition()
          .duration(800)
          .attr('y', d => y(d.value))
          .attr('height', d => innerHeight - y(d.value))
          .delay((d, i) => i * 100);

        bars.append('text')
          .attr('class', 'label')
          .attr('x', d => x(d.label) + x.bandwidth() / 2)
          .attr('y', d => y(d.value) - 13)
          .attr('dy', '.75em')
          .text(d => d.value)
          .attr('text-anchor', 'middle')
          .style('font-size', '16px')
          .style('font-weight', 'bold');
      })
      .catch(error => {
        console.error('Error loading the CSV file:', error);
      });
  }

  function handleButtonClick(country) {
    selectedCountry = country;
    updateChart(country);
    updateHeading(country);
    select('#countryDropdown').property('value', country);
  }

  onMount(() => {
    const svg = select('#my_dataviz')
      .append('svg')
      .attr('width', width)
      .attr('height', height)
      .append('g')
      .attr('transform', `translate(${margin.left},${margin.top})`);

    fetch("/drinks.csv")
      .then(response => response.text())
      .then(text => {
        const data = d3.csvParse(text);
        data.forEach(d => {
          d.beer_servings = +d.beer_servings;
          d.spirit_servings = +d.spirit_servings;
          d.wine_servings = +d.wine_servings;
          d.total_litres_of_pure_alcohol = +d.total_litres_of_pure_alcohol;
        });

        countries = data.map(d => d.country);
        countries.sort();

        updateChart(selectedCountry);
        updateHeading(selectedCountry);

        const dropdown = select('#countryDropdown');

        dropdown.selectAll('option')
          .data(countries)
          .enter()
          .append('option')
          .text(d => d)
          .attr('value', d => d);

        dropdown.property('value', selectedCountry);

        dropdown.on('change', function() {
          selectedCountry = this.value;
          updateChart(selectedCountry);
          updateHeading(selectedCountry);
        });
      })
      .catch(error => {
        console.error('Error loading the CSV file:', error);
      });
  });
</script>

<style>
  .user {
    text-align: center;
    font-size: 18px;
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

  .tooltip {
    position: absolute;
    background-color: lightgray;
    border: 1px solid gray;
    padding: 5px;
    font-size: 16px;
    pointer-events: none;
    opacity: 0;
    max-width: 200px;
    word-wrap: break-word;
  }

  .container {
    display: flex;
    flex-direction: column;
    align-items: center;
    text-align: center;
  }

  #my_dataviz {
    display: flex;
    justify-content: center;
    align-items: center;
  }

  svg {
    display: block;
    margin: auto;
  }

  .sidebar {
    display: flex;
    flex-direction: column;
    align-items: flex-start;
    margin-right: 20px;
  }

  .sidebar button {
    margin-bottom: 10px;
    padding: 10px;
    font-size: 16px;
    cursor: pointer;
  }

  .grid line {
    stroke: lightgrey;
    stroke-opacity: 0.7;
    shape-rendering: crispEdges;
  }

  .grid path {
    stroke-width: 0;
  }

  .chart-area {
    display: flex;
    flex-direction: column;
    align-items: center;
  }

  .content {
    display: flex;
    justify-content: center;
  }

  .buttons {
    display: flex;
    flex-direction: column;
    align-items: flex-start;
    margin-left: 20px;
  }

  .buttons h3 {
    margin-bottom: 10px;
  }
</style>

<div class="container">
  <h1 class="body-header">Alcohol Type Consumption by Country in Modern Day</h1>

  <p class="body-text">
    With an understanding of how alcohol consumption began in ancient times, let us explore how it looks in modern day.
  </p>

  <p class="user">
    <strong>Now it's your turn to explore!</strong>
  </p>

  <p class="body-text">
    Choose a country from the dropdown 
    to generate a bar chart describing different consumption rates of different 
    alcohol types of your chosen country.
  </p>

  <p class="body-text">
    The original data set can be found from this 
    <a href="https://github.com/fivethirtyeight/data/tree/master/alcohol-consumption">fivethirtyeight</a> link. 
  </p>

  <h2 id="chartHeading1">Alcohol Consumption in USA</h2>

  <div class="content">
    <div class="chart-area">
      <div>
        <label for="countryDropdown">Select a country: </label>
        <select id="countryDropdown"></select>
      </div>
      <div id="my_dataviz"></div>
    </div>

    <div class="sidebar">
      <h3>Records</h3>
      {#each buttonRecords as record}
        <button on:click={() => handleButtonClick(record.country)}>{record.text}</button>
      {/each}
    </div>
  </div>
</div>

