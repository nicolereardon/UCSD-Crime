<link href="https://api.mapbox.com/mapbox-gl-js/v2.7.0/mapbox-gl.css" rel="stylesheet" />

<script>
    let input_center = [-117.23685272044901, 32.87940699468673];
</script>

<main>
    
    <div class="map-container">
        <svg class="map-svg" viewBox="0 0 800 400">
            <!-- Embed the map image here -->
            <image href={`https://api.mapbox.com/styles/v1/mapbox/light-v11/static/${input_center[0]},${input_center[1]},13,0,0/400x400?access_token=pk.eyJ1Ijoia3NrYW5la28iLCJhIjoiY2xzZm4ycm01MGtjYTJqcHFsMXl1enNjcCJ9.20jJnxwrWnDVKl-EZOGVew`} width="100%" height="100%" />
            <foreignObject x="5%" y="23%" width="90%" height="100%">
                <div class="conclusion">
                    <h1>
                        The existing Timely Alerts offer swift reactions to crucial occurrences <span class="highlight"> and </span>
                        provide students and faculty with vital details regarding said events.
                        <span class="highlight"> However</span>, the counterintuitive layout complicates information retrieval, 
                        posing a risk to user safety. <span class="highlight"> Therefore</span>, we propose enhancements to the current
                        design alongside visual aids to empower users in forming nuanced perspectives.
                    </h1>
                </div>
            </foreignObject>
        </svg>
    </div>
</main>

<style>
    .highlight {
        color: #5494DA; 
    }
    .map-container {
        width: 100%;
        height: 100%;
    }

    .map-svg {
        width: 100%;
        border: 1px solid #ddd;
    }

    .conclusion {
        position: absolute;
        text-align: center;
        z-index: 1;
        font-size: 12px;
        font-family: 'Roboto', sans-serif;
    }
</style>