<script>
	import * as d3 from "d3";
	import { onMount } from 'svelte';

    export let data;
	// console.log(data)
	let categoryData = [];
    let pieData = [];

    let svg;

    onMount(() => {
        categoryData = d3.rollup(data, v => v.length, d => d['CrimeCategory']);
        pieData = Array.from(categoryData, ([category, count]) => ({ category, count }));
		console.log(pieData)
        createPieChart(pieData);
	});

	function createPieChart(pieData) {
        const margin = { top: 20, right: 20, bottom: 20, left: 20 };
        const width = 1100 - margin.left - margin.right;
        const height = 600 - margin.top - margin.bottom;

        // Check if there's an existing SVG, remove it if present
        if (svg) {
            d3.select('#pie-chart-container svg').remove();
        }

        svg = d3
            .select('#pie-chart-container')
            .append('svg')
            .attr('width', width + margin.left + margin.right)
            .attr('height', height + margin.top + margin.bottom)
            .append('g')
            .attr('transform', `translate(${width / 2},${height / 2})`); // Center the pie chart

        // Create a pie chart layout
        const pie = d3.pie().value(d => d.count);

        // Define an arc generator
        const arc = d3.arc().outerRadius(Math.min(width, height) / 2 - 10).innerRadius(0);
        // Generate pie chart slices
        const arcs = svg
            .selectAll('arc')
            .data(pie(pieData))
            .enter()
            .append('g')
            .attr('class', 'arc');

        // Append path elements for each slice
        arcs
            .append('path')
            .attr('d', arc)
            .attr('fill', (d, i) => color(i)); // You may want to define a color scale

        // Append text labels
        arcs
            .append('text')
            .attr('transform', d => `translate(${arc.centroid(d)})`)
            .attr('dy', '0.35em')
            .attr('text-anchor', 'middle')
            .text(d => d.data.location);
    }
    createPieChart(pieData);
</script>


<style>
    
</style>

<div id="pie-chart-container">
    <!-- svg will be appended here -->
</div>