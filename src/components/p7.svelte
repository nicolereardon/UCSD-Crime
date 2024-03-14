<link href="https://api.mapbox.com/mapbox-gl-js/v2.7.0/mapbox-gl.css" rel="stylesheet" />

<script>
    let input_center = [-117.23685272044901, 32.87940699468673];
</script>

<main>
    <h1>Map View</h1>
    
    <div class="map-container">
        <svg class="map-svg" viewBox="0 0 400 400">
            <!-- Embed the map image here -->
            <image href={`https://api.mapbox.com/styles/v1/mapbox/light-v11/static/${input_center[0]},${input_center[1]},13,0,0/400x400?access_token=pk.eyJ1Ijoia3NrYW5la28iLCJhIjoiY2xzZm4ycm01MGtjYTJqcHFsMXl1enNjcCJ9.20jJnxwrWnDVKl-EZOGVew`} width="100%" height="100%" />
        </svg>
    </div>
</main>

<div id="top-right-text">
    Let's get this to a better spot and figure out what to say
</div>

<style>
    #top-right-text {
        position: absolute;
        top: 140px;
        left: calc(50%);
        transform: translateX(50%);
        width: 300px; /* Adjust as needed */
        border: 3px solid #ADD8E6;
        padding: 10px;
        background-color: white;
        font-family: 'Lato', sans-serif;
        line-height: 1.3;
        font-size: 18px;
    }

    .map-container {
        width: 100%; /* Adjust the map size */
        margin: auto; /* Center horizontally */
    }

    .map-svg {
        width: 100%;
        border: 1px solid #ddd;
    }
</style>