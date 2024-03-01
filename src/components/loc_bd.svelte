<script>
    import * as d3 from 'd3';
    import { onMount } from 'svelte';

    export let data;

    let locationData = [];
    let barData = [];

    let svg;

    onMount(() => {
		const filteredData = data.filter(d => ((d.coords !== "") && (d.Update === ""))); //filter to have coord and not be an update :D

        locationData = d3.rollup(filteredData, v => v.length, d => d['MapGroup']);
        barData = Array.from(locationData, ([location, count]) => ({ location, count }));
		barData.sort((a, b) => b.count - a.count); 
        createBarChart(barData);
    });

    function createBarChart(barData) {
        const margin = { top: 30, right: 30, bottom: 150, left: 60 };
        const width = 1300 - margin.left - margin.right;
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

        const x = d3.scaleBand().range([0, width]).padding(0.2);
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

		// Add x-axis label
		svg.append('text')
            .attr('transform', `translate(${width / 2},${height + margin.top + 40})`) // Adjusted position
            .style('text-anchor', 'middle')
            .text('Locations');

        // Add y-axis label
        svg.append('text')
            .attr('transform', 'rotate(-90)')
            .attr('y', 0 - margin.left)
            .attr('x', 0 - height / 2)
            .attr('dy', '1em')
            .style('text-anchor', 'middle')
            .text('Frequency');

        // Add title
        svg.append('text')
            .attr('x', width / 2)
            .attr('y', 0 - margin.top / 2)
            .attr('text-anchor', 'middle')
            .style('font-size', '20px')
            .text('Taking a look at Timely Warning Locations');
    }
</script>

<style>
    
</style>

<div id="bar-chart-container">
    <!-- svg will be appended here -->
</div>