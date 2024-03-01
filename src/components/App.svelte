<script>

  import { onMount } from 'svelte';
  import * as d3 from 'd3';
  import Loc_bd from '../components/loc_bd.svelte';
  import P3 from '../components/p3.svelte';
  import Map from "./Map.svelte";

  let data = [];
  let map_data = [];
  let counter = 1;
  let isOverButton = false;

  onMount(async () => {
    try {
      const res = await fetch('final_data.csv');
      const csv = await res.text();
      data = d3.csvParse(csv, (d) => {
        return {
          Key: +d.Key,
          AlertCategory: d.AlertCategory,
          DateOfResponse: d.DateOfResponse,
          TimeOfResponse: d.TimeOfResponse,
          DateOfCrime: d.DateOfCrime,
          TimeOfCrime: d.TimeOfCrime,
          CrimeCategory: d.CrimeCategory,
          LocationOfCrime: d.LocationOfCrime,
          SuspectDescription: d.SuspectDescription,
          SuspectInCustody: d.SuspectInCustody,
          Update: d.Update,
          Link: d.Link,
          MapGroup: d.MapGroup,
          coords: d.coords,
          latitude: +d.latitude,
          longitude: +d.longitude
        };
      });

      //we used to have map_data a condensed version of data, but this will actually create problems 
      //once we try and add the pie chart hover, so I think I'll instead pass data into Map and make Map work it out.
      
      const res2 = await fetch('map_data.csv');
      const csv2 = await res2.text();
      map_data = d3.csvParse(csv2, (d) => {
        return {
          map_group: d.map_group,
          longitude: +d.longitude,
          latitude: +d.latitude,
          count: +d.count
        };
      });
      //console.log(map_data);


    } catch (error) {
      console.error('Data loading error:', error);
    }
  });


  function page1(){
    counter = 1;
  }

  function page2(){
    counter = 2;
  }

  function page3(){
    counter = 3;
  }

  function incrementpage(){
    if (!isOverButton) {
      return;
    }
  }

  $: {
    console.log(counter);
  }
</script>

<main on:click={incrementpage}>
  <h1>DSC106 Final Project Prototype</h1>

  <a href="Writeup.html" target="_blank"> Click here for Writeup!</a>


  <div class="info-box">
    {#if counter===1}
        <Map {data} />
    {:else if counter===2}
        <Loc_bd {data} />
    {:else if counter===3}
        <P3 {data}/>
    {/if}
  </div>

  <button type="button" on:click={page1} on:mouseenter={() => isOverButton = true} on:mouseleave={() => isOverButton = false}> Page 1</button>
  <button type="button" on:click={page2} on:mouseenter={() => isOverButton = true} on:mouseleave={() => isOverButton = false}> Page 2</button>
  <button type="button" on:click={page3} on:mouseenter={() => isOverButton = true} on:mouseleave={() => isOverButton = false}> Page 3</button>
</main>

<style>
  .info-box {
    margin-top: 20px;
    margin-bottom: 50px;
    margin-left: 20px;
    margin-right: 20px;
    padding: 10px;
    border: 1px solid #ccc;
  }
</style>