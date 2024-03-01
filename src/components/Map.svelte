<script>
  import { onMount } from "svelte";
  import mapboxgl from "mapbox-gl";
  import * as d3 from "d3";
  import { pie, arc } from "d3";

  let map;
  export let data;

  let svg;
  let categoryData = [];
  let pieData = [];

  onMount(() => {
    mapboxgl.accessToken = "pk.eyJ1Ijoia3NrYW5la28iLCJhIjoiY2xzZm4ycm01MGtjYTJqcHFsMXl1enNjcCJ9.20jJnxwrWnDVKl-EZOGVew";

    map = new mapboxgl.Map({
      container: "map",
      style: "mapbox://styles/mapbox/light-v11",
      center: [-117.23685272044901, 32.87930699468673],
      zoom: 13.5,
      minZoom: 12,
      maxZoom: 25,
    });

    // for pie charts:
    categoryData = d3.rollup(data, v => v.length, d => d['CrimeCategory']);
    pieData = Array.from(categoryData, ([category, count]) => ({ category, count }));
		
    map.on("load", () => {
      addCircleLayer();
      createPieChart(pieData);
    });
  });

  function addCircleLayer() {
  const filteredData = data.filter(d => (d.coords !== "") && (d.Update === ""));

  const locationData = d3.rollup(filteredData, v => v.length, d => d['MapGroup']);
  const map_data = Array.from(locationData, ([map_group, count]) => {
  const groupData = filteredData.find(d => d['MapGroup'] === map_group);

  if (groupData) {
    return {
      map_group,
      count,
      latitude: +groupData.latitude,
      longitude: +groupData.longitude,
      AlertCategory: groupData.AlertCategory
    };
  } else {
    console.error(`No data found for map_group ${map_group}`);
    return null;
  }
}).filter(entry => entry !== null);

const features = map_data.map(d => {
  if (!isNaN(d.latitude) && !isNaN(d.longitude)) {
    return {
      type: "Feature",
      properties: {
        map_group: d.map_group,
        count: d.count,
        AlertCategory: d.AlertCategory
      },
      geometry: {
        type: "Point",
        coordinates: [d.longitude, d.latitude]
      }
    };
  } else {
    console.error(`Invalid coordinates for map_group ${d.map_group}`);
    return null;
  }
}).filter(feature => feature !== null);

  map.addSource("mapGroups", {
    type: "geojson",
    data: {
      type: "FeatureCollection",
      features: features
    }
  });

  map.addLayer({
    id: "circleLayer",
    source: "mapGroups",
    type: "circle",
    paint: {
      "circle-radius": [
        "interpolate",
        ["linear"],
        ["get", "count"],
        0, 5,
        d3.max(map_data, d => d.count), 30
      ],
      "circle-color": "steelblue",
      "circle-opacity": 0.7
    }
  });

  // Add a hover effect to display pie chart on circle hover
  map.on("mouseenter", "circleLayer", (e) => {
    const features = map.queryRenderedFeatures(e.point, { layers: ["circleLayer"] });

    if (features.length > 0) {
      const feature = features[0];
      console.log("Hovered Feature:", feature);
      showPieChart(feature.properties.map_group);
    }
  });

  // Reset the pie chart when mouse leaves the circle
  map.on("mouseleave", "circleLayer", () => {
    hidePieChart();
  });
}

  function showPieChart(map_group) {
    // Code to display pie chart based on the 'map_group' value
    // You can customize this part based on your specific requirements
    console.log("Show Pie Chart for Map Group:", map_group);
  }

  function hidePieChart() {
    // Code to hide or reset the pie chart
    // You can customize this part based on your specific requirements
    console.log("Hide/Reset Pie Chart");
  }

  function createPieChart(pieData) {
        const margin = { top: 20, right: 20, bottom: 20, left: 20 };
        const width = 400;
        const height = 450;
        const mapContainer = document.getElementById('map');
        const mapContainerRect = mapContainer.getBoundingClientRect();
        console.log(height)

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
            .attr('transform', `translate(${175},${375})`); // Center the pie chart

        // Create a pie chart layout
        const pie = d3.pie().value(d => d.count);

        // Define an arc generator
        const arc = d3.arc().outerRadius(100).innerRadius(0);
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
            // .attr('fill', (d, i) => color(i)); // You may want to define a color scale

        // Append text labels
        arcs
            .append('text')
            .attr('transform', d => `translate(${arc.centroid(d)})`)
            .attr('dy', '0.35em')
            .attr('text-anchor', 'middle')
            .text(d => d.data.location);
    }
</script>

<div id='container'>
  <div id="map" class="map"></div>
  <div id="pie-chart-container"></div>
</div>

<style>
  .map {
    position: relative;
    width: 800px;
    height: 500px;
  }
  #pie-chart-container {
    width: 400px; /* Adjust the width as needed */
    height: 500px;
    float: right; /* or display: inline-block; */
  }

  #container {
    display: flex; /* Use flexbox for layout */
  }
</style>