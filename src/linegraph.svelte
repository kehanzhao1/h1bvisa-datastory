<script>
 import * as d3 from 'd3';
 import { onMount} from 'svelte';
import App from './App.svelte';
export let value;

 let linemargin = { top: 10, right: 50, bottom: 30, left: 100 };
 let linewidth = 500 - linemargin.left - linemargin.right;
 let lineheight = 500 - linemargin.top - linemargin.bottom;
    //let chartW = width - margin.left - margin.right;
   // let chartH = height - margin.top - margin.bottom;

let datal  = []

    // Scales
let xScale, yScale, colorScale;
let svgElement;
const formatComma = d3.format(",");
const formatComma2 = d3.format(".2f");

onMount(async () => {

const rawData = await d3.csv('./h1b_approval_denial.csv', d=> ({
            ...d,
            'FY': +d['FY'],
            'Registration': +d['Registration'],
            'lottery': +d['lottery'],
            'cap': +d['cap']
        }));
        datal = [];
     rawData.forEach(d => {
      datal.push({ Year: d['FY'], Registrations: d['Registration'], lottery_rate: d['lottery'], cap: d['cap'] });
    });
    console.log(datal);
    clearGraph();
    renderLineGraph()
      });


      $: if (value === 0) {
    console.log('Value is 0, rendering first graph'); // Debug value
    clearGraph();
    //svgElement.selectAll(".lenged").remove();  
    renderLineGraph();
   
      } else if (value === 1) {
    console.log('Value is 1, rendering lottery rate graph'); // Debug value
    clearGraph();
    createLotteryRateGraph();
  }
  function clearGraph() {
    if (svgElement) {
      svgElement.selectAll("*").remove();
      
    }
  }
      function renderLineGraph() {
    // Set up scales 
   //clearGraph();
   
    console.log('Creating first graph');
    xScale = d3.scaleLinear()
      .domain(d3.extent(datal, d => d.Year))
      .range([0, linewidth]);

    yScale = d3.scaleLinear()
    .domain([0, d3.max(datal, d => Math.max(d.Registrations))])
    .range([lineheight, 0]);

    colorScale = d3.scaleOrdinal()
    .domain(['Registrations', 'cap'])
    .range(['darkSalmon', 'steelBlue']); // Colors for the lines

    // Create SVG element
    svgElement = d3.select("#linegraph")
      .attr("width", linewidth + linemargin.left + linemargin.right)
      .attr("height", lineheight + linemargin.top + linemargin.bottom)
      .append("g")
      .attr("transform", `translate(${linemargin.left},${linemargin.top})`);


    // Add X axis
    svgElement.append("g")
      .attr("transform", `translate(0,${lineheight})`)
      .call(d3.axisBottom(xScale).tickFormat(d3.format("d")))
      .selectAll("text")
      .style("font-size", "14px"); 

    // Add Y axis
    svgElement.append("g")
      .call(d3.axisLeft(yScale).tickFormat(formatComma))
      .selectAll("text")
      .style("font-size", "14px"); 

      svgElement.selectAll(".domain, .tick line")
      .style("stroke-width", "2px")
        .style("stroke", "darkGrey");
    // Add line for registrations
    const lineRegistration = d3.line()
      .x(d => xScale(d.Year))
      .y(d => yScale(d.Registrations));

      const pathRegistration = svgElement.append("path")
      .datum(datal)
      .attr("fill", "none")
      .attr("stroke", colorScale('Registrations'))
      .attr("stroke-width", 3)
      .attr("d", lineRegistration);
      
    console.log('Path for registrations created:', pathRegistration); 


      svgElement.selectAll(".dot-registration")
      .data(datal)
      .enter().append("circle")
      .attr("class", "dot-registration")
      .attr("cx", d => xScale(d.Year))
      .attr("cy", d => yScale(d.Registrations))
      .attr("r", 5)
      .attr("fill", colorScale('Registrations'))
      .on("mouseover", function(event, d) {
        console.log('Mouseover event:', d);
        tooltip.transition()
          .duration(200)
          .style("opacity", .9);
        tooltip.html(`Year: ${d.Year}<br>Registrations: ${formatComma(d.Registrations)}${d.Year == '2024' ? '<br><em>High due to fraud and duplicate registrations</em>' : ''}`)
          .style("left", (event.pageX - tooltip.node().offsetWidth - 10) + "px")
          .style("top", (event.pageY - 10) + "px");
      })
      .on("mouseout", function(d) {
        console.log('Mouseout event:', d);
        tooltip.transition()
          .duration(200)
          .style("opacity", 0);
      });


       // Add line for cap
    const lineCap = d3.line()
      .x(d => xScale(d.Year))
      .y(d => yScale(d.cap));

      
    const pathCap = svgElement.append("path")
      .datum(datal)
      .attr("fill", "none")
      .attr("stroke", colorScale('cap'))
      .attr("stroke-width", 2)
      .attr("d", lineCap);
      console.log('Path for cap created:', pathCap);
    svgElement.append("path")
      .datum(datal)
      .attr("fill", "none")
      .attr("stroke", colorScale('cap'))
      .attr("stroke-width", 2)
      .attr("d", lineCap);

      svgElement.selectAll(".dot-cap")
      .data(datal)
      .enter().append("circle")
      .attr("class", "dot-cap")
      .attr("cx", d => xScale(d.Year))
      .attr("cy", d => yScale(d.cap))
      .attr("r", 4)
      .attr("fill", colorScale('cap'));

         // Add legend
         const legend = svgElement.selectAll(".legend")
      .data(colorScale.domain())
      .enter().append("g")
      .attr("class", "legend")
      .attr("transform", (d, i) => `translate(30,${i * 28+20})`);

    legend.append("circle")
      .attr("cx", 6)
      .attr("cy", 6)
      .attr("r", 8)
      .style("fill", colorScale);

    legend.append("text")
      .attr("x", 24)
      .attr("y", 9)
      .attr("dy", ".35em")
      .style("text-anchor", "start")
      .text(d => d.charAt(0).toUpperCase() + d.slice(1))
      .style("font-size", "20px"); 
      
      const totalLengthRegistration = pathRegistration.node().getTotalLength();
    pathRegistration
      .attr("stroke-dasharray", `${totalLengthRegistration} ${totalLengthRegistration}`)
      .attr("stroke-dashoffset", totalLengthRegistration)
      .transition()
      .duration(1000)
      .ease(d3.easeLinear)
      .attr("stroke-dashoffset", 0);

 // Add line for lottery
 //const lineLottery = d3.line()
   //   .x(d => xScale(d.year))
     // .y(d => yScale(d.lottery));

    //svgElement.append("path")
      //.datum(datal)
      //.attr("fill", "none")
      //.attr("stroke", colorScale('lottery'))
      //.attr("stroke-width", 1.5)
      //.attr("d", lineLottery);
  

  }

    
  function createLotteryRateGraph() {
    // Clear the existing graph
     clearGraph();
    console.log('Creating lottery rate graph'); 
    //svgElement.selectAll("*").remove();

    // Update yScale domain to include lottery_rate
    yScale.domain([0, d3.max(datal, d => d.lottery_rate)]);

    // Update Y axis
    svgElement.append("g")
      .call(d3.axisLeft(yScale).tickFormat(formatComma))
      .selectAll("text")
      .style("font-size", "14px");

      
    // Add X axis
    svgElement.append("g")
      .attr("transform", `translate(0,${lineheight})`)
      .call(d3.axisBottom(xScale).tickFormat(d3.format("d")))
      .selectAll("text")
      .style("font-size", "14px");
      svgElement.selectAll(".domain, .tick line")
      .style("stroke-width", "2px")
        .style("stroke", "darkGrey");
    // Add line for lottery_rate
    const lineLotteryRate = d3.line()
      .x(d => xScale(d.Year))
      .y(d => yScale(d.lottery_rate));

    const pathLotteryRate = svgElement.append("path")
      .datum(datal)
      .attr("fill", "none")
      .attr("stroke", "darkgreen")
      .attr("stroke-width", 3)
      .attr("d", lineLotteryRate);

    console.log('Path for lottery_rate created:', pathLotteryRate); // Print path element to the console

    // Animate lines
    if (pathLotteryRate.node()) {
      const totalLengthLotteryRate = pathLotteryRate.node().getTotalLength();
      pathLotteryRate
        .attr("stroke-dasharray", `${totalLengthLotteryRate} ${totalLengthLotteryRate}`)
        .attr("stroke-dashoffset", totalLengthLotteryRate)
        .transition()
        .duration(1000)
        .ease(d3.easeLinear)
        .attr("stroke-dashoffset", 0);
    } else {
      console.error('Path for lottery_rate is null');
    };
    
    // Add data points for lottery_rate
    svgElement.selectAll(".dot-lottery")
      .data(datal)
      .enter().append("circle")
      .attr("class", "dot-lottery")
      .attr("cx", d => xScale(d.Year))
      .attr("cy", d => yScale(d.lottery_rate))
      .attr("r", 5)
      .attr("fill", "green")
      .on("mouseover", function(event, d) {
        tooltip.transition()
          .duration(100)
          .style("opacity", .9);
        tooltip.html(`Year: ${d.Year}<br>Lottery selection rate: ${formatComma2(d.lottery_rate)}`)
          .style("left", (event.pageX - tooltip.node().offsetWidth - 10) + "px")
          .style("top", (event.pageY - 10) + "px");
      })
      .on("mouseout", function(d) {
        console.log('Mouseout event:', d);
        tooltip.transition()
          .duration(200)
          .style("opacity", 0);
      });

      //Add legend for lottery_rate
    const legend2 = svgElement.selectAll(".legend")
      .data(['lottery_rate'])
      .enter().append("g")
      .attr("class", "legend")
      .attr("transform", (d, i) => `translate(30,${i * 28 + 5})`);

    legend2.append("circle")
      .attr("cx", 6)
      .attr("cy", 6)
      .attr("r", 8)
      .style("fill", "green");

    legend2.append("text")
      .attr("x", 24)
      .attr("y", 9)
      .attr("dy", ".35em")
      .style("text-anchor", "start")
      .text("Lottery selection rate")
      .style("font-size", "20px");
  }
  

   
  const tooltip = d3.select("body").append("div")
    .attr("class", "tooltip")
    .style("opacity", 0)
    .style("position", "absolute")
      .style("background", "#fff")
      .style("border", "1px solid #ccc")
      .style("padding", "10px")
</script>

<svg id="linegraph"></svg>

<style>
  svg {
    font-family: 'Lato', sans-serif;
  }
  .tooltip {
    position: absolute;
    text-align: center;
    width: auto;
    height: auto;
    padding: 8px;
    font: 12px sans-serif;
    background: lightsteelblue;
    border: 0px;
    border-radius: 8px;
    pointer-events: none;
  }
</style>
