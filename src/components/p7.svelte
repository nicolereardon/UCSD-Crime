<link href="https://api.mapbox.com/mapbox-gl-js/v2.7.0/mapbox-gl.css" rel="stylesheet" />

<script>
    let input_center = [-117.23685272044901, 32.87940699468673];
</script>

<main>
    <h1>Map View</h1>
    
    <svg class="map-svg" viewBox="0 0 500 500">
        <!-- Embed the map image here -->
        <image href={`https://api.mapbox.com/styles/v1/mapbox/light-v11/static/${input_center[0]},${input_center[1]},13,0,0/400x400?access_token=pk.eyJ1Ijoia3NrYW5la28iLCJhIjoiY2xzZm4ycm01MGtjYTJqcHFsMXl1enNjcCJ9.20jJnxwrWnDVKl-EZOGVew`} width="100%" height="100%" />
    </svg>
</main>

<style>
    .map-svg {
        width: 100%;
        border: 1px solid #ddd;
    }
</style>