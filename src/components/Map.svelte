<script>
  import { onMount } from "svelte";
  import mapboxgl from "mapbox-gl";
  import * as d3 from "d3";
  import { pie, arc } from "d3";

  let map;
  export let data;

  onMount(() => {
    mapboxgl.accessToken = "pk.eyJ1Ijoia3NrYW5la28iLCJhIjoiY2xzZm4ycm01MGtjYTJqcHFsMXl1enNjcCJ9.20jJnxwrWnDVKl-EZOGVew";

    map = new mapboxgl.Map({
      container: "map",
      style: "mapbox://styles/mapbox/light-v11",
      center: [-117.23185272044901, 32.87830699468673],
      zoom: 13,
      minZoom: 12,
      maxZoom: 25,
    });

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
</script>

<div id="map" class="map"></div>

<style>
  .map {
    position: relative;
    width: 100%;
    height: 600px;
  }
</style>