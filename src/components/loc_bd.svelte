<script>
    import * as d3 from 'd3';
    import { onMount } from 'svelte';

    export let data;

    let locationData = [];
    let barData = [];

    let svg;

    onMount(() => {
        locationData = d3.rollup(data, v => v.length, d => d['MapGroup']);
        barData = Array.from(locationData, ([location, count]) => ({ location, count }));
		console.log(barData)
        createBarChart(barData);
    });

    function createBarChart(barData) {
        const margin = { top: 20, right: 20, bottom: 90, left: 40 };
        const width = 1100 - margin.left - margin.right;
        const height = 600 - margin.top - margin.bottom;

        // Check if there's an existing SVG, remove it if present
        if (svg) {
            d3.select('#bar-chart-container svg').remove();
        }

        svg = d3
            .select('#bar-chart-container')
            .append('svg')
            .attr('width', width + margin.left + margin.right)
            .attr('height', height + margin.top + margin.bottom)
            .append('g')
            .attr('transform', `translate(${margin.left},${margin.top})`);

        const x = d3.scaleBand().range([0, width]).padding(0.1);
        const y = d3.scaleLinear().range([height, 0]);

        x.domain(barData.map(d => d.location));
        y.domain([0, d3.max(barData, d => d.count)]);

        svg
            .selectAll('.bar')
            .data(barData)
            .enter()
            .append('rect')
            .attr('class', 'bar')
            .attr('x', d => x(d.location))
            .attr('width', x.bandwidth())
            .attr('y', d => y(d.count))
            .attr('height', d => height - y(d.count));

        svg
            .append('g')
            .attr('transform', `translate(0,${height})`)
            .call(d3.axisBottom(x))
			.selectAll('text')
            .attr('transform', 'rotate(-45)')
            .style('text-anchor', 'end');

        svg.append('g').call(d3.axisLeft(y));
    }
</script>

<style>
    
</style>

<div id="bar-chart-container">
    <!-- svg will be appended here -->
</div>