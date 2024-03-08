<link href="https://api.mapbox.com/mapbox-gl-js/v2.7.0/mapbox-gl.css" rel="stylesheet" />

<script>
    import Map_bg from './Map_bg.svelte';

    export let data;

    let center1 = [-117.23685272044901, 32.87930699468673];
    let center2 = [-117.23685272044901, 32.87930699468673];
</script>

<main class="container">
    <h1>Recent Crimes</h1>

    <!-- First Map Container -->
    <div id="map1" class="map-container">
        <!-- Use Mapbox Static Images API to generate a static image -->
        <img src={`https://api.mapbox.com/styles/v1/mapbox/light-v11/static/${center1[0]},${center1[1]},13.5,0,0/400x400?access_token=pk.eyJ1Ijoia3NrYW5la28iLCJhIjoiY2xzZm4ycm01MGtjYTJqcHFsMXl1enNjcCJ9.20jJnxwrWnDVKl-EZOGVew`} alt="Map 1" />
    </div>

    <!-- Second Map Container -->
    <div id="map2" class="map-container">
        <!-- Use Mapbox Static Images API to generate a static image -->
        <img src={`https://api.mapbox.com/styles/v1/mapbox/light-v11/static/${center2[0]},${center2[1]},13.5,0,0/400x400?access_token=pk.eyJ1Ijoia3NrYW5la28iLCJhIjoiY2xzZm4ycm01MGtjYTJqcHFsMXl1enNjcCJ9.20jJnxwrWnDVKl-EZOGVew`} alt="Map 2" />
    </div>

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