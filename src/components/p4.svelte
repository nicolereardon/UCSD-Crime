<script>
    import { onMount } from "svelte";
    import mapboxgl from "mapbox-gl";
    import * as d3 from "d3";
    import { pie, arc } from "d3";

    let map;
    let quarters;
    let timeScale;
    export let data;

    let svg;
    let categoryData = [];
    let pieData = [];
    let currentMapGroup = null;

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
        quarters = ['WI2019', 'SP2019', 'SU2019', 'FA2019', 'WI2020', 'SU2020', 'FA2020', 'WI2021', 'SU2021', 'FA2021', 'WI2022', 'SP2022', 'SU2022', 'FA2022', 'WI2023', 'SP2023', 'SU2023', 'FA2023', 'WI2024'];

        timeScale = d3.scaleOrdinal()
            .domain(quarters)
            .range(d3.range(0, 1, 1 / quarters.length));

        addCircleLayer();
        });
    });
    function addCircleLayer() {
        const filteredData = data.filter(d => (d.coords !== "") && (d.Update === ""));
        
        const locationData = d3.rollup(filteredData, v => v.length, d => d['MapGroup'], d => d['quarter']);
        const map_data = Array.from(locationData, ([map_group, quartersMap]) => {
            const groupData = filteredData.find(d => d['MapGroup'] === map_group);

            if (groupData) {
                const quarterData = Array.from(quartersMap).find(([quarter, count]) => quarter === 'WI2024');
                return {
                    map_group,
                    quarters: quarterData ? [{ quarter: 'WI2024', count: quarterData[1] }] : [], // Adjust the structure as needed
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
        console.log(map_data);

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
                    0, 5,
                    d3.max(map_data, d => d.count), 20
                ],
                "circle-color": "steelblue",
                "circle-opacity": 0.7
            }
        });
    }

    function update_station_markers() {
		station_markers
        	.transition()
			.duration(1000)
			.attr("r", function (d) {
				let trafficVolume =
					arrivals[d["station_id"]] + departures[d["station_id"]];
				return scaleRadiusTrafficVolume(trafficVolume);
			})
			.style("fill", function(d) { 
				let totalTraffic = 
					arrivals[d["station_id"]] + departures[d["station_id"]]; 
				let arrivalRatio = 
					arrivals[d["station_id"]] / totalTraffic; 
				return interpolateColor(arrivalRatio); 
			}); 
		}
</script>

<div id='container'>
    <div id="map" class="map"></div>
</div>

<main>
    <!-- <div class="overlay">
		<label>{slider_label}</label>
		<input
			id="slider"
			type="range"
			min="1"
			max="1440"
			bind:value={slider_time}
		/>
	</div> -->

    <p>Nicole add's her visualization</p>
    <p>I don't know why but this container doesn't get bigger with just the other div tag</p>
    <p>I'll figure it out later</p>
    <div id="top-right-text" 
        style="position: absolute; top: 120px; left: 700px; right: 60px; font-size: 14px; border: 1px solid #ccc; padding: 10px;">
            <strong>Step 3:</strong> Map the Terrain - Know where these incidents 
            unfold and their frequency in specific locations. Navigate away from high-risk zones.
    </div>
</main>

<style>
</style>