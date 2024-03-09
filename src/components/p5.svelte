<link href="https://api.mapbox.com/mapbox-gl-js/v2.7.0/mapbox-gl.css" rel="stylesheet" />

<script>

    export let data;
    let mostRecentCrimes;

    mostRecentCrimes = data.slice(-2);

    let center1 = [mostRecentCrimes[0].longitude, mostRecentCrimes[0].latitude];
    let center2 = [mostRecentCrimes[1].longitude, mostRecentCrimes[1].latitude];
</script>

<main class="container">
    <h1>Recent Crimes</h1>

    <!-- First Map Container -->
    <div id="map1" class="map-container">
        <!-- Use Mapbox Static Images API to generate a static image -->
        <img src={`https://api.mapbox.com/styles/v1/mapbox/dark-v11/static/${center1[0]},${center1[1]},16,0,0/400x400?access_token=pk.eyJ1Ijoia3NrYW5la28iLCJhIjoiY2xzZm4ycm01MGtjYTJqcHFsMXl1enNjcCJ9.20jJnxwrWnDVKl-EZOGVew`} alt="Map 1" />
    </div>

    <!-- Second Map Container -->
    <div id="map2" class="map-container">
        <!-- Use Mapbox Static Images API to generate a static image -->
        <img src={`https://api.mapbox.com/styles/v1/mapbox/dark-v11/static/${center2[0]},${center2[1]},16,0,0/400x400?access_token=pk.eyJ1Ijoia3NrYW5la28iLCJhIjoiY2xzZm4ycm01MGtjYTJqcHFsMXl1enNjcCJ9.20jJnxwrWnDVKl-EZOGVew`} alt="Map 2" />
    </div>

    <div id="top-right-text" style="position: absolute; top: 120px; 
        right: 60px; font-size: 14px; border: 1px solid #ccc; 
        padding: 10px;">
            Top Right Text</div>

    <style>
        .container {
            display: flex;
            flex-direction: column;
            align-items: center;
            justify-content: center;
        }

        .map-container {
            width: 400px;
            height: 400px;
            margin: 10px;
        }
    </style>
</main>