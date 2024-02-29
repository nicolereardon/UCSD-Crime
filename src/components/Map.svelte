<script>
  import { onMount, onDestroy } from "svelte";
  import mapboxgl from "mapbox-gl";

  let map;

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
  });

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
