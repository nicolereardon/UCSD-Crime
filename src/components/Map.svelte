<script>
    import mapboxgl from "mapbox-gl";
    import { onMount, onDestroy } from "svelte";

    let map;
    let mapContainer;
    let lng, lat, zoom;

    lng = 32.8812;
    lat = 32.8812;
    zoom = 9;

    onMount(() => {
    const initialState = { lng: lng, lat: lat, zoom: zoom };

    mapboxgl.accessToken = "pk.eyJ1IjoiYmFzb243NyIsImEiOiJjbHNqcTVnam4xbWk4MnFubjlxbDZ6emxyIn0.LrXfvcHh7ksjHGLXAyrgZA";
	const map = new mapboxgl.Map({
		container: mapContainer,
		style: "mapbox://styles/mapbox/light-v11", 
		center: [initialState.lng, initialState.lat],
        zoom: initialState.zoom,
		minZoom: 12,
		maxZoom: 15,
	});
});

onDestroy(() => {
  map.remove();
});

</script>

<div class="map-wrap">
    Page 3 Stuff
  <div class="map" bind:this={mapContainer} />
</div>

<style>
    .map {
     position: absolute;
     width: 100%;
     height: 100%;
    }
</style>