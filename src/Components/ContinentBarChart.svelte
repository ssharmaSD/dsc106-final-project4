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

  let selectedContinent = 'North America';
  let continents = [];

  function updateHeading(continent) {
    select('#chartHeading').text(`Alcohol Consumption in ${continent}`);
  }

  onMount(() => {
    const svg = select('#cont_dataviz')
      .append('svg')
      .attr('width', width)
      .attr('height', height)
      .attr('viewBox', `0 0 ${width} ${height}`)
      .style('margin', 'auto')
      .style('display', 'block')
      .append('g')
      .attr('transform', `translate(${margin.left},${margin.top})`);

    fetch("/continent.csv")
      .then(response => response.text())
      .then(text => {
        const data = d3.csvParse(text);
        data.forEach(d => {
          d.beer_servings = +d.beer_servings;
          d.spirit_servings = +d.spirit_servings;
          d.wine_servings = +d.wine_servings;
          d.total_litres_of_pure_alcohol = +d.total_litres_of_pure_alcohol;
        });

        continents = Array.from(new Set(data.map(d => d.continent)));
        continents.sort();

        updateChart(selectedContinent);
        updateHeading(selectedContinent);

        const dropdown = select('#continentDropdown');

        dropdown.selectAll('option')
          .data(continents)
          .enter()
          .append('option')
          .text(d => d)
          .attr('value', d => d);

        dropdown.property('value', selectedContinent);

        dropdown.on('change', function() {
          selectedContinent = this.value;
          updateChart(selectedContinent);
          updateHeading(selectedContinent);
        });

        function updateChart(continent) {
          const continentData = data.filter(d => d.continent === continent);
          if (continentData.length === 0) {
            console.error('Continent not found in data.');
            return;
          }

          const aggregatedData = continentData.reduce((acc, d) => {
            acc.beer_servings += d.beer_servings;
            acc.spirit_servings += d.spirit_servings;
            acc.wine_servings += d.wine_servings;
            return acc;
          }, { beer_servings: 0, spirit_servings: 0, wine_servings: 0 });

          const barData = [
            { label: 'Beer Servings', value: aggregatedData.beer_servings, color: '#a35108' },
            { label: 'Spirit Servings', value: aggregatedData.spirit_servings, color: '#a8e1e3' },
            { label: 'Wine Servings', value: aggregatedData.wine_servings, color: '#8a0101' },
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
        }
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

  #my_dataviz {
    display: flex;
    justify-content: center;
    align-items: center;
  }

  svg {
    display: block;
    margin: auto;
  }

  .container {
    display: flex;
    flex-direction: column;
    align-items: center;
    text-align: center;
  }

  .container label,
  .container select {
    margin-top: 10px;
  }

  h2 {
    margin-bottom: 10px;
  }

  .body-header {
    text-align: center;
  }
</style>

<div class="container">
  <h1 class="body-header">Alcohol Type Consumption by Continent in Modern Day</h1>
  <p class="body-text">
    With an understanding of how alcohol consumption began in ancient times, let us explore how it looks in modern day.
  </p>

  <p class="user">
    <strong>Now it's your turn to explore!</strong>
  </p>

  <p class="body-text">
    Choose a continent from the dropdown 
    to generate a bar chart describing different consumption rates of different 
    alcohol types of your chosen continent.
  </p>

  <p class="body-text">
    The original data set can be found from this 
    <a href="https://github.com/fivethirtyeight/data/tree/master/alcohol-consumption">fivethirtyeight</a> link. 
  </p>

  <p class="body-text">
    <strong>Note:</strong> We have had a lot of trouble trying to get this bar chart to center,
    any guidance that could be provided on this would be greatly appreciated. Thanks!
  </p>

  <h2 id="chartHeading">Alcohol Consumption in North America</h2>
  <div>
    <label for="continentDropdown">Select a continent: </label>
    <select id="continentDropdown"></select>
  </div>
  <div id="cont_dataviz"></div>
</div>
