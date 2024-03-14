<script>
    import * as d3 from 'd3';
    import { onMount } from 'svelte';

    export let data;

    let crimeData = [];
    let barData = [];

    let crimeText = {
    'Burglary': 'Burglary is a crime of breaking and entering a building illegally with the intent to commit a crime, typically theft.',
    'Arson': 'Arson involves intentionally setting fire to property, often with criminal intent.',
    'Robbery': 'Robbery is the act of taking or attempting to take something from someone by force or threat of force.',
    'Attempted Burglary': 'Attempted Burglary refers to an unsuccessful attempt to break into a building with the intent to commit a crime.',
    'Aggravated Assault': 'Aggravated Assault is a more severe form of assault, often involving a weapon or causing serious injury.',
    'Sexual Battery': 'Sexual Battery is a criminal offense involving non-consensual sexual touching or penetration.',
    'Suspicious Activity': 'Suspicious Activity refers to behavior that raises concerns and may indicate criminal or harmful intent.',
    'Avoid Area': 'Avoid Area warnings suggest staying away from a specific location due to potential danger or ongoing incidents.',
    'Indecent Exposure': 'Indecent Exposure involves the exposure of one\'s genitals in a public place, often with lewd intent.',
    'Motor Vehicle Theft': 'Motor Vehicle Theft is the unauthorized taking of someone\'s vehicle with the intent to permanently deprive them of it.',
    'Sexual Assault': 'Sexual Assault is a broader term that encompasses various non-consensual sexual acts.',
    'Attempted Robbery': 'Attempted Robbery refers to an unsuccessful attempt to take something from someone by force or threat of force.',
    'Weapons Law Violation': 'Weapons Law Violation involves the violation of laws related to the possession or use of weapons.',
    'Brandishing of a Firearm': 'Brandishing of a Firearm is the act of displaying a firearm in a threatening manner.',
    'Attempted Motor Vehicle Theft': 'Attempted Motor Vehicle Theft is an unsuccessful attempt to steal a motor vehicle.',
    'Stalking': 'Stalking is a pattern of unwanted attention, harassment, or other behavior intended to create fear or distress.',
    'Attempted E-Bike / Motor Vehicle Theft': 'Attempted E-Bike / Motor Vehicle Theft is an unsuccessful attempt to steal an e-bike or motor vehicle.',
    '': 'This category may indicate missing or incomplete information about the reported crime.',
    'Intimidation - Sexual Orientation Bias': 'Intimidation - Sexual Orientation Bias involves actions intended to intimidate or harm someone based on their sexual orientation.',
    'Forced Entry Into Occupied Residence': 'Forced Entry Into Occupied Residence refers to entering a residence forcefully while it is occupied.',
    'Attempted Strong-Arm Robbery': 'Attempted Strong-Arm Robbery is an unsuccessful attempt to forcefully take something from someone without a weapon.',
    'Sexual Assault Unknown Perpetrator': 'Sexual Assault by an Unknown Perpetrator involves non-consensual sexual acts where the perpetrator is unidentified.',
    'Information on Jacobs Medical Center Incident': 'This category may contain information about an incident at the Jacobs Medical Center.',
    'Trespassing in an Occupied Residence': 'Trespassing in an Occupied Residence is the unauthorized entry into an occupied residence.',
    'Office Building Burglaries': 'Office Building Burglaries involve unauthorized entry into office buildings with the intent to commit a crime.',
    'Missing Juvenile At Risk': 'Missing Juvenile At Risk indicates the disappearance of a juvenile who may be in danger.',
    'Demonstration': 'A group of people congregating to express opposition to a practice or issue.'
};

    let svg;

    onMount(() => {
		const filteredData = data.filter(d => ((d.coords !== "") && (d.Update === ""))); 
        //not be an update, got rid of must have coord req

        crimeData = d3.rollup(filteredData, v => v.length, d => d['CrimeCategory']);
        barData = Array.from(crimeData, ([crime, count]) => ({ crime, count }));
		barData.sort((a, b) => b.count - a.count); 
        console.log(barData)
        createBarChart(barData);
    });

    function createBarChart(barData) {
        const margin = { top: 30, right: 30, bottom: 150, left: 60 };
        const width = 1300 - margin.left - margin.right;
        const height = 550 - margin.top - margin.bottom;

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

        x.domain(barData.map(d => d.crime));
        y.domain([0, d3.max(barData, d => d.count)]);

        svg
    .selectAll('.bar')
    .data(barData)
    .enter()
    .append('rect')
    .attr('class', 'bar')
    .attr('x', d => x(d.crime))
    .attr('width', x.bandwidth())
    .attr('y', d => y(d.count))
    .attr('height', d => height - y(d.count))
    .attr('fill', '#006D77')
    .on('mouseover', handleMouseOver)
    .on('mouseout', handleMouseOut);

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
            .attr('transform', `translate(${width / 2},${height + margin.top + 100})`) // Adjusted position to make fit
			.attr('font-weight', "bold")
            .style('text-anchor', 'middle')
            .text('Crimes');

        // Add y-axis label
        svg.append('text')
            .attr('transform', 'rotate(-90)')
            .attr('y', 0 - margin.left)
            .attr('x', 0 - height / 2)
            .attr('dy', '1em')
			.attr('font-weight', "bold")
            .style('text-anchor', 'middle')
            .text('Frequency');


    

    function handleMouseOver(event, d) {
    // Show tooltip
    const tooltip = document.getElementById('tooltip');
    tooltip.style.opacity = 0.9;

    // Adjust the color of the hovered bar
    d3.select(this).attr('fill', '#E29578'); // Adjust the color as needed

    // Position the tooltip slightly to the right of the mouse
    const [x, y] = d3.pointer(event);
    tooltip.style.left = x + 10 + 'px';
    tooltip.style.top = y + 'px';

    // Update tooltip content based on the hovered data
    tooltip.innerHTML = `<strong>${d.crime}</strong>: ${d.count} occurrences<br>${crimeText[d.crime] || 'Description not available'}`;
}

    function handleMouseOut(event, d) {
        // Hide tooltip
        const tooltip = document.getElementById('tooltip');
        tooltip.style.opacity = 0;

        // Restore the original color of the bar
        d3.select(this).attr('fill', '#006D77'); // Restore the original color
    }

    }
</script>

<style>
    #subtitle {
        font-size: 28px;
        font-family: 'Roboto', sans-serif;
        margin-left: 50px;
    }

    .tooltip {
        position: absolute;
        background-color: white;
        border: 1px solid #ccc;
        padding: 10px;
        pointer-events: none;
        font-size: 20px;
        font-family: "EB Garamond", serif;
        box-shadow: 0 0 10px rgba(0, 0, 0, 0.2);
    }

    #top-right-text {
        position: absolute;
        top: 140px; 
        left: 900px; 
        right: 60px;
        border: 3px solid #ADD8E6; 
        padding: 10px; 
        background-color: white;
        font-family: 'Lato', sans-serif;
            line-height: 1.3;
        font-size: 18px;
    }
</style>

<h1 id="subtitle">Overall Crime Categories</h1>
<div id="bar-chart-container">
    <!-- svg will be appended here -->
    <div id="tooltip" class="tooltip" style="opacity: 0;"></div>
    <div id="top-right-text" >
        Let's begin by understanding which crimes or events meet the threshold to warrant a Timely Warning 
        or Community Alert Bulletin. To the left is a bar chart that shows the frequency of the different 
        alerts given over the past 5 years. They may vary in terms of specificity, but by hovering over a bar,
        you have access to a brief description of what that crime or alert means. The most frequent types of 
        crimes are burglary, arson, and robbery.
    </div>

</div>