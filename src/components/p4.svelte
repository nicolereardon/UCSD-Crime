<script>
    import { onMount, afterUpdate } from "svelte";
    import mapboxgl from "mapbox-gl";
    import * as d3 from "d3";

    let map;
    export let data;
    let sliderValue = 10;
    let selectedQuarter = 'WI2022';
    let quarters = ['WI2019', 'SP2019', 'SU2019', 'FA2019', 'WI2020', 'SU2020', 'FA2020', 'WI2021', 'SU2021', 'FA2021', 'WI2022', 'SP2022', 'SU2022', 'FA2022', 'WI2023', 'SP2023', 'SU2023', 'FA2023', 'WI2024'];
    let qvals = {
        0: 'WI2019', 
        1: 'SP2019', 
        2: 'SU2019', 
        3: 'FA2019', 
        4: 'WI2020', 
        5: 'SU2020', 
        6: 'FA2020', 
        7: 'WI2021', 
        8: 'SU2021', 
        9: 'FA2021', 
        10: 'WI2022', 
        11: 'SP2022', 
        12: 'SU2022', 
        13: 'FA2022', 
        14: 'WI2023', 
        15: 'SP2023', 
        16: 'SU2023', 
        17: 'FA2023', 
        18: 'WI2024'
    }


    onMount(() => {
        mapboxgl.accessToken = "pk.eyJ1Ijoia3NrYW5la28iLCJhIjoiY2xzZm4ycm01MGtjYTJqcHFsMXl1enNjcCJ9.20jJnxwrWnDVKl-EZOGVew";

        map = new mapboxgl.Map({
        container: "map",
        style: "mapbox://styles/mapbox/light-v11",
        center: [-117.23485272044901, 32.87930699468673],
        zoom: 13.5,
        minZoom: 12,
        maxZoom: 25,
        });

        map.on("load", () => {
            addCircleLayer();
        });
    });

    // afterUpdate(() => {
    //     updateCircleLayer();
    // });

    function addCircleLayer() {
        const filteredData = data.filter(d => (d.coords !== "") && (d.Update === ""));

        const locationData = d3.rollup(filteredData, v => v.length, d => d['MapGroup'], d => d['quarter']);
        const map_data = Array.from(locationData, ([map_group, quartersMap]) => {
            const groupData = filteredData.find(d => d['MapGroup'] === map_group);

            if (groupData) {
                const quarterData = Array.from(quartersMap).find(([quarter, count]) => quarter === selectedQuarter);
                return {
                    map_group,
                    quarters: quarterData ? [{ quarter: selectedQuarter, count: quarterData[1] }] : [], // Adjust the structure as needed
                    latitude: +groupData.latitude,
                    longitude: +groupData.longitude,
                    AlertCategory: groupData.AlertCategory,
                    count: quarterData ? quarterData[1] : 0 // Get count for 'WI2024'
                };
            } else {
                // console.error(`No data found for map_group ${map_group}`);
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
                // console.error(`Invalid coordinates for map_group ${d.map_group}`);
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
                    0, 3,
                    d3.max(map_data, d => d.count), d3.max(map_data, d => d.count) + 10
                ],
                "circle-color": "steelblue",
                "circle-opacity": 0.7
            }
        });
    }



    function updateCircleLayer() {
    // Remove existing layer and source
    if (map.getLayer('circleLayer')) {
        map.removeLayer('circleLayer');
    }

    const filteredData = data.filter(d => (d.coords !== "") && (d.Update === ""));
    const locationData = d3.rollup(filteredData, v => v.length, d => d['MapGroup'], d => d['quarter']);
    const map_data = Array.from(locationData, ([map_group, quartersMap]) => {
    const groupData = filteredData.find(d => d['MapGroup'] === map_group);

    if (groupData) {
            const quarterData = Array.from(quartersMap).find(([quarter, count]) => quarter === selectedQuarter);
            return {
                map_group,
                quarters: quarterData ? [{ quarter: selectedQuarter, count: quarterData[1] }] : [], // Adjust the structure as needed
                latitude: +groupData.latitude,
                longitude: +groupData.longitude,
                AlertCategory: groupData.AlertCategory,
                count: quarterData ? quarterData[1] : 0 // Get count for 'WI2024'
            };
        } else {
            // console.error(`No data found for map_group ${map_group}`);
            return null;
        }
    }).filter(entry => entry !== null);

    console.log("hi")
    map.addLayer({
        id: "circleLayer",
        source: "mapGroups",
        type: "circle",
        paint: {
            "circle-radius": [
                "interpolate",
                ["linear"],
                ["get", "count"],
                0, 3,
                d3.max(map_data, d => d.count), d3.max(map_data, d => d.count) + 10
            ],
            "circle-color": "steelblue",
            "circle-opacity": 0.7
        }
    });
}
</script>

<main>
    <h1 id="subtitle">Crime Breakdown by Quarter</h1>

    <!-- <div id="gif">
        <img src='pg4gif.gif' alt="Crimes Over Time" style="width: 60%; height: auto;"/>
    </div> -->
    <div id="top-right-text" >
        In addition to understand where crimes occur, you may also be curious of when. By taking a look at how this disruption of
        crimes has changed overtime, we are able to understand what areas are becoming more or less safe.
    </div>
    <div id='container'>
        <!-- Slider UI -->
        <div class="slider-container">
            <label for="quarter-slider">Quarter: </label>
            <input
                id="quarter-slider"
                type="range"
                min="0"
                max="18"
                step="1"
                bind:value={sliderValue}
                on:input={() => {
                    selectedQuarter = qvals[sliderValue];
                    updateCircleLayer();
                }}
            />
            <span>{selectedQuarter}</span>
        </div>
        <div id="map" class="map"></div>
    </div>
</main>

<style>
    #top-right-text {
        position: absolute;
        top: 140px; 
        left: 870px; 
        right: 60px;
        border: 3px solid #ADD8E6; 
        padding: 10px; 
        background-color: white;
        font-family: "EB Garamond", serif;
        font-size: 18px;
    }

    #subtitle {
        font-size: 28px;
        font-family: "Whisper", cursive;
        margin-left: 180px;
    }
    .map {
        position: left;
        width: 800px;
        height: 500px;
        margin-right: 20px; /* Adjust this value to move the map to the left */
    }

    #container {
        display: flex;
        flex-direction: column;
        align-items: center;
    }

    .slider-container {
        margin-top: 20px;
        text-align: center;
    }
</style>
