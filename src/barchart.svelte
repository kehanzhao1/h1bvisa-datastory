<script>
  import * as d3 from 'd3';
  import { onMount } from 'svelte';
  export let value;
  
  let margin = { top: 80, right: 50, bottom: 90, left: 100 };
 let width = 500 - margin.left - margin.right;
 let height = 600 - margin.top - margin.bottom;

  let data = [];
  let svgElement;
  

  onMount(async () => {
    try {
      const rawData = await d3.csv('./h1b_breakdown.csv', d => ({
        country: d['Country'],
        percent_country: +d['Percent_country']/100,
        occupations: d['Occupations'],
        percent_occ: +d['Percent_occ']/100
      }));

      data = rawData.filter(d => d.percent_country !== null || d.percent_occ !== null);
      console.log('Filtered data:', data); 
      console.log('Loaded data2:', data); // Debug data
     // drawBarChart('#countryChart', data.slice(0, 6), 'country', 'percent_country', 'darksalmon');
      //drawBarChart('#occupationChart', data, 'occupations', 'percent_occ','steelblue');
    } catch (error) {
      console.error('Error loading data:', error);
    }
  });
  function clearChart(selector) {
    const svg = d3.select(selector);
    svg.selectAll("*").remove();
  };
  function clearGraph() {
    if (svgElement) {
      svgElement.selectAll("*").remove();
      
    }
  }
  $: if (value === 2) {
    console.log('Value is 2, rendering 3rd graph')// Debug value
    clearGraph();
    svgElement = d3.select('#chart');
    //svgElement.selectAll(".lenged").remove();  
    drawBarChart('#chart', data.slice(0, 6), 'country', 'percent_country', 'darksalmon', 'Country of Applicants');
   
      } else if (value === 3) {
       // clearGraph();
    console.log('Value is 3, rendering 4th graph'); // Debug value
    clearGraph();
    //clearChart(selector);
    svgElement = d3.select('#chart');
    drawBarChart('#chart', data.slice(0, 7), 'occupations', 'percent_occ','steelblue','Occupation of Applicants');
  }

  function drawBarChart(selector, data, xField, yField, barColor, title) {
    //clearChart(selector); 
    const svg = d3.select(selector)
      .attr('width', width + margin.left + margin.right)
      .attr('height', height + margin.top + margin.bottom)
      .append('g')
      .attr('transform', `translate(${margin.left},${margin.top})`);

      svg.append('text')
      .attr('x', (width / 2))             
      .attr('y', 0 - (margin.top / 2))
      .attr('text-anchor', 'middle')  
      .style('font-size', '24px') 
      .style('font-family', 'Lato, sans-serif')
      .text(title);

    const x = d3.scaleBand()
      .domain(data.map(d => d[xField]))
      .range([0, width])
      .paddingInner(0.1)
      .paddingOuter(0.1);

    const y = d3.scaleLinear()
      .domain([0, 1])
      .nice()
      .range([height, 0]);

    svg.append('g')
      .attr('class', 'x-axis')
      .attr('transform', `translate(0,${height})`)
      .call(d3.axisBottom(x).tickSizeOuter(0))
      .selectAll('path, line')
      .attr('stroke', 'darkgray')
      .attr('stroke-width', 2);

      svg.selectAll('.x-axis text')
      .attr('transform', 'rotate(315)')
      .style('text-anchor', 'end');
      

    svg.append('g')
      .attr('class', 'y-axis')
      .call(d3.axisLeft(y))
      .call(d3.axisLeft(y).tickFormat(d3.format(".0%")))
      .selectAll('path, line')
      .attr('stroke', 'darkgray')
      .attr('stroke-width', 2);

      svg.selectAll('.x-axis text, .y-axis text')
      .style('font-family', 'Lato, sans-serif')
      .style('font-size', '14px');

    svg.selectAll('.bar')
      .data(data)
      .enter().append('rect')
      .attr('class', 'bar')
      .attr('x', d => x(d[xField]))
      .attr('y', height)
      .attr('width', x.bandwidth())
      .attr('height',0)
      .attr('fill', barColor)
      .transition() // Add transition
      .duration(500) // Duration of the animation
      .attr('y', d => y(d[yField])) // Transition to the final y position
      .attr('height', d => height - y(d[yField])); // Transition to the final height


      svg.selectAll('.text')
      .data(data)
      .enter().append('text')
      .attr('class', 'label')
      .attr('x', d => x(d[xField]) + x.bandwidth() / 2)
      .attr('y', height)
      .attr('height', 0)
      .attr('text-anchor', 'middle')
      .style('font-family', 'Lato, sans-serif')
      .style('font-size', '16px')
      .style('fill', barColor)
      .style('font-weight', 'bold')
      .text(d => d3.format(".1%")(d[yField]))
      .transition() // Add transition
      .duration(500) // Duration of the animation
      .attr('y', d => y(d[yField])-8) // Transition to the final y position
      .attr('height', d => height - 20- y(d[yField])); // Transition to the final height
  }
</script>

<style>
 

  .x-axis path,
  .x-axis line,
  .y-axis path,
  .y-axis  {
    stroke: darkgray;
    stroke-width: 2px;
  }

  svg.x-axis text,
  svg.y-axis text {
    font-family: 'Lato', sans-serif; /* Set font to Lato */
    font-size: 14px; /* Make text bigger */
  }
</style>

<svg id="chart"></svg>