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
  let currentMapGroup = null;

  const colorScale = d3.scaleOrdinal(d3.schemeCategory10);

  onMount(() => {
    mapboxgl.accessToken = "pk.eyJ1Ijoia3NrYW5la28iLCJhIjoiY2xzZm4ycm01MGtjYTJqcHFsMXl1enNjcCJ9.20jJnxwrWnDVKl-EZOGVew";

    map = new mapboxgl.Map({
      container: "map",
      style: "mapbox://styles/mapbox/light-v11",
      center: [-117.23485272044901, 32.87930699468673],
      zoom: 13.5,
      minZoom: 12,
      maxZoom: 25,
    });

    // for pie charts:
    categoryData = d3.rollup(data, v => v.length, d => d['CrimeCategory']);
    pieData = Array.from(categoryData, ([category, count]) => ({ category, count }));
		
    map.on("load", () => {
      addCircleLayer();
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
        d3.max(map_data, d => d.count), 20
      ],
      "circle-color": "red",
      "circle-opacity": 0.7
    }
  });

  // Add a hover effect to display pie chart on circle hover
  map.on("mouseenter", "circleLayer", (e) => {
      const features = map.queryRenderedFeatures(e.point, { layers: ["circleLayer"] });

      if (features.length > 0) {
        const feature = features[0];
        console.log("Hovered Feature:", feature);

        const mapGroup = feature.properties.map_group;
        console.log("Hovered Map Group:", mapGroup);

        showPieChart(mapGroup);
      }
    });

    map.on("mouseleave", "circleLayer", () => {
      hidePieChart();
    });
  }

  function showPieChart(mapGroup) {
    const imageUrl = `${mapGroup}.png`; // Assuming your PNG files are named after MapGroup categories
    // Update the src attribute of the image
    const imgElement = document.getElementById("pie-chart-image");
    if (imgElement) {
      imgElement.src = imageUrl;
    }
    console.log("Show PNG Image for Map Group:", mapGroup);
  }

  function hidePieChart() {
    // Reset or hide the displayed PNG image
    const imgElement = document.getElementById("pie-chart-image");
    if (imgElement) {
      imgElement.src = "main_chart.png"; // Reset the src attribute
    }
    console.log("Hide/Reset PNG Image");
  }
</script>

<h1 id="subtitle">Crime Breakdown by Location</h1>

<div id='container'>
  <div id="map-container">
    <div id="map" class="map"></div>
  </div>
  <div id="info-text-container">
    <div id="top-right-text">
      Now we would like to know where these crimes and disruptions occur, so we have a better idea of what areas
      might be more or less dangerous than others. To the left we have an interactive map that can be dragged around to look at
      different areas. The circles show areas where crimes occur, with the size corresponding to how many incidents have occurred.
      To the right of the map we have a pie chart that breaks down the types of crimes that have occurred. When no dot is
      being hovered this shows a breakdown of all the crimes, and while a dot is hovered it shows a breakdown of just the crimes
      for that area.
    </div>
    <!-- Add an image element for displaying the PNG -->
    <img src="main_chart.png" alt="Pie Chart" id="pie-chart-image" style="width: 500px; height: 350px;">
  </div>
</div>

<style>
  #subtitle {
        font-size: 28px;
        font-family: 'Roboto', sans-serif;
        margin-left: 50px;
    }

  .map {
    width: 700px;
    height: 500px;
  }

  #container {
    display: flex;
    align-items: flex-start; /* Align items to the top */
  }

  #map-container {
    margin-right: 20px; /* Add some spacing between the map and the pie chart */
  }

  #info-text-container {
    display: flex;
    flex-direction: column;
    align-items: center; 
  }

  #top-right-text {
    margin-top: -50px;
    margin-bottom: 30px;
    border: 3px solid #ADD8E6;
    padding: 10px;
    background-color: white;
    font-family: 'Lato', sans-serif;
            line-height: 1.3;
    font-size: 18px;
    max-width: 700px;
  }
</style>