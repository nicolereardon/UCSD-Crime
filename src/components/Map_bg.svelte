<script>
  import { onMount } from "svelte";
  import mapboxgl from "mapbox-gl";
  import * as d3 from "d3";

  let map;
  export let data;
  export let input_center;

  let svg;
  let categoryData = [];

  onMount(() => {
    mapboxgl.accessToken = "pk.eyJ1Ijoia3NrYW5la28iLCJhIjoiY2xzZm4ycm01MGtjYTJqcHFsMXl1enNjcCJ9.20jJnxwrWnDVKl-EZOGVew";

    map = new mapboxgl.Map({
      container: "map",
      style: "mapbox://styles/mapbox/light-v11",
      center: input_center,
      zoom: 15,
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
          d3.max(map_data, d => d.count), 30
        ],
        "circle-color": "red",
        "circle-opacity": 0.7
      }
    });
  }
</script>

<div id='container'>
  <div id="map" class="map"></div>
</div>

<style>
  body, html {
    margin: 0;
    padding: 0;
    width: 100%;
    height: 100%;
    overflow: hidden;
  }

  #container {
    position: absolute;
    top: 0px; /* Adjust this value according to your title height */
    left: 0;
    right: 0;
    bottom: 0;
    pointer-events: none; /* Allow clicking through the map to elements underneath */

  }

  .map {
    position: absolute;
    top: 0;
    left: 0;
    right: 0;
    bottom: 0;
    z-index: -1000;
    background-color: rgba(255, 255, 255, 0.8);
    pointer-events: auto;
  }

</style>