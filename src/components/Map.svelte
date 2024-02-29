<script>
  import { onMount, onDestroy } from "svelte";
  import mapboxgl from "mapbox-gl";
  import * as d3 from "d3";

  let map;
  export let map_data;

  onMount(() => {
    mapboxgl.accessToken = "pk.eyJ1Ijoia3NrYW5la28iLCJhIjoiY2xzZm4ycm01MGtjYTJqcHFsMXl1enNjcCJ9.20jJnxwrWnDVKl-EZOGVew";
    map = new mapboxgl.Map({
      container: "map",
      style: "mapbox://styles/mapbox/light-v11",
      center: [-117.23185272044901, 32.87830699468673],
      zoom: 13,
      minZoom: 12,
      maxZoom: 15,
    });
    
    map.on("load", () => {
      addCircleLayer();
    });
  });

  onDestroy(() => {
    if (tooltip) {
      tooltip.remove();
    }
  });

  function addCircleLayer() {
    map.addSource("mapGroups", {
      type: "geojson",
      data: {
        type: "FeatureCollection",
        features: map_data.map(d => ({
          type: "Feature",
          properties: {
            map_group: d.map_group,
            count: +d.count
          },
          geometry: {
            type: "Point",
            coordinates: [+d.longitude, +d.latitude]
          }
        }))
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

  }

</script>

<div id="map" class="map"></div>

<style>
  /* Write your CSS here */
  .map {
    position: relative;
    width: 100%;
    height: 400px;
  }

</style>
