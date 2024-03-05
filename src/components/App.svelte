<script>

  import { onMount } from 'svelte';
  import * as d3 from 'd3';
  //import Loc_bd from '../components/loc_bd.svelte';
  import Crime_bd from '../components/crime_bd.svelte';
  import P3 from '../components/p3.svelte';
  import Map from "./Map.svelte";
  import Map_bg from "./Map_bg.svelte";
  import { fade } from 'svelte/transition';

  let data = [];
  // let map_data = [];
  let counter = 0;
  let isOverButton = false;
  let showIntro = true;

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

      // Hide the intro screen after a delay (e.g., 5 seconds)
      setTimeout(() => {
        showIntro = false;
        if (counter < 1) {
          counter = 1;
        }
      }, 5000);
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
      if (counter !== 3) {
        counter += 1
      }
      return;
    }
  }

  $: {
    console.log(counter);
  }
</script>

<main on:click={incrementpage}>
  {#if showIntro}
    <div in:fade={{ duration: 500, delay: 0 }} out:fade={{ duration: 250, delay: 0 }} class = "intro_page">
      <h1 class="intro_text">Want to stay safe at UCSD?</h1>
      <button class="learn_more_button" on:click={() => (showIntro = false)}>Learn more</button>
      <Map_bg {data} />
    </div>
  {:else}
  <div in:fade={{ duration: 250, delay: 1000 }} class = "mainpages">
    <h1>DSC106 Final Project Prototype</h1>

    <a href="Writeup.html" target="_blank"> Click here for Writeup!</a>


    {#if counter < 1}
      <h1>We might not need this page but just in case</h1>
      //maybe go back to intro
    {/if}
  
    {#if counter >= 1}
      <div class="info-box">
        {#if counter===1}
          <P3 {data}/>
        {:else if counter ===2}
            <Crime_bd {data} />
        {:else if counter===3}
            <Map {data} />
        {:else if counter===4}
           
        {/if}
      </div>

    <button type="button" on:click={page1} on:mouseenter={() => isOverButton = true} on:mouseleave={() => isOverButton = false}> Page 1</button>
    <button type="button" on:click={page2} on:mouseenter={() => isOverButton = true} on:mouseleave={() => isOverButton = false}> Page 2</button>
    <button type="button" on:click={page3} on:mouseenter={() => isOverButton = true} on:mouseleave={() => isOverButton = false}> Page 3</button>
    {/if}

  </div>
  {/if}
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

  .intro_page {
    display: flex;
    flex-direction: column;
    justify-content: center;
    align-items: center;
    height: 100vh; /* Set to 100% of the viewport height */
  }

  .intro_text {
    font-size: 2em; /* Adjust the font size as needed */
    text-align: center;
    margin-bottom: 20px; /* Add space between h1 and button */
  }

  .learn_more_button {
    background-color: black;
    color: white;
    border: none;
    border-radius: 25px; /* Adjust the border-radius to make it oval */
    padding: 10px 20px; /* Adjust padding as needed */
    font-size: 1.2em; /* Adjust the font size as needed */
    cursor: pointer;
  }

  .learn_more_button:hover {
    background-color: #333; /* Darken the background on hover if desired */
  }
</style>