<script>
    import { onMount, afterUpdate } from "svelte";
    import mapboxgl from "mapbox-gl";
    import * as d3 from "d3";

    let map;
    export let data;
    let sliderValue = 20;
    let showError = false;
    let by_quarter = false;
    let selectedQuarter = 'WI2024';
    // let quarters = ['WI2019', 'SP2019', 'SU2019', 'FA2019', 'WI2020', 'SU2020', 'FA2020', 'WI2021', 'SU2021', 'FA2021', 'WI2022', 'SP2022', 'SU2022', 'FA2022', 'WI2023', 'SP2023', 'SU2023', 'FA2023', 'WI2024'];
    let qvals = {
        0: 'WI2019', 
        1: 'SP2019', 
        2: 'SU2019', 
        3: 'FA2019', 
        4: 'WI2020', 
        5: 'SP2020',
        6: 'SU2020', 
        7: 'FA2020', 
        8: 'WI2021',
        9: 'SP2021', 
        10: 'SU2021', 
        11: 'FA2021', 
        12: 'WI2022', 
        13: 'SP2022', 
        14: 'SU2022', 
        15: 'FA2022', 
        16: 'WI2023', 
        17: 'SP2023', 
        18: 'SU2023', 
        19: 'FA2023', 
        20: 'WI2024'
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
        const filteredData = data.filter(d => (d.coords !== "") && (d.Update === "") && (d.quarter === selectedQuarter));
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
                        d3.max(map_data, d => d.count), d3.max(map_data, d => d.count) * 4
                    ],
                "circle-color": "red",
                "circle-opacity": 0.7
            }
        });
    }

    function updateCircleLayer() {
        // Remove existing layer
        if (map.getLayer('circleLayer')) {
            map.removeLayer('circleLayer');
        }
        if (map.getSource("mapGroups")) {
            map.removeSource("mapGroups");
        }
        hideNone()
        const filteredData = data.filter(d => (d.coords !== "") && (d.Update === "") && (d.quarter === selectedQuarter));
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

        if (map_data.some(entry => entry.count !== 0)) {
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
                        d3.max(map_data, d => d.count), d3.max(map_data, d => d.count) * 4
                    ],
                    "circle-color": "red",
                    "circle-opacity": 0.7
                }
            });
        } else {
            showNone()
        }
    }

    function showNone() {
        showError = true;
    }

    function hideNone() {
        showError = false;
    }

    function to_quarter() {
        by_quarter = true;
    }
    
    function to_count() {
        by_quarter = false;
    }
</script>

<main>
    <h1 id="subtitle">Crime Breakdown by Quarter</h1>

    <div id="top-right-text" >
        In addition to understand where crimes occur, you may also be curious of when. By taking a look at how this disruption of
        crimes has changed overtime, we are able to understand what areas are becoming more or less safe.
    </div>

    {#if showError}
        <div id="no-alert">
            No location-specific Triton Alerts reported in this quarter.
        </div>
    {/if}

    <div id='container'>
        <!-- Slider UI -->
        <div class="slider-container" on:click|stopPropagation={() => {}}>
            <label for="quarter-slider">Quarter:</label>
            <input
                id="quarter-slider"
                type="range"
                min="0"
                max="20"
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
        <button class = "button1" type="button" on:click={to_quarter} on:click|stopPropagation={() => {}}> By Quarter </button>
        <button class = "button2" type="button" on:click={to_count} on:click|stopPropagation={() => {}}> By Count </button>

        {#if by_quarter}
            <div class = "charts">
                <img src="by_quarter.png" alt="Chart" id="by_quarter" style="width: 160px; height: 440px;">
            </div>
        {:else}
            <div class = "charts">
                <img src="by_count.png" alt="Chart" id="by_count" style="width: 160px; height: 440px;">
            </div>
        {/if}
    </div>
</main>

<style>
    #top-right-text {
        position: absolute;
        top: 140px; 
        left: 970px; 
        right: 60px;
        border: 3px solid #ADD8E6; 
        padding: 10px; 
        background-color: white;
        font-family: 'Lato', sans-serif;
            line-height: 1.3;
        font-size: 18px;
    }

    #no-alert {
        position: absolute;
        top: 450px; 
        left: 300px; 
        width: 500px;
        border: 3px solid #e69393; 
        padding: 10px; 
        background-color: white;
        font-family: 'Roboto', sans-serif;
        font-size: 18px;
        text-align: center;
    }

    #subtitle {
        font-size: 28px;
        font-family: 'Roboto', sans-serif;
        margin-left: 50px;
    }
    .map {
        margin: 0 auto;
        margin-left: 100px;
        position: center;
        width: 800px;
        height: 500px;
    }

    .charts {
        position: absolute;
        top: 280px; 
        left: 970px;
    }

    .button1 {
        position: absolute;
        top: 540px; 
        left: 1170px;
        width: 80px;
        height: 30px;
    }

    .button2 {
        position: absolute;
        top: 500px; 
        left: 1170px;
        width: 80px;
        height: 30px;
    }

    #container {
        display: flex;
        flex-direction: column;
        align-items: flex-start;
    }

    .slider-container {
        margin: 0 auto;
        margin-left: 400px;
        margin-top: 20px;
        text-align: center;
    }
</style>
