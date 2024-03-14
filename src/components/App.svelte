<script>

  import { onMount } from 'svelte';
  import * as d3 from 'd3';
  //import Loc_bd from '../components/loc_bd.svelte';
  import Crime_bd from '../components/crime_bd.svelte';
  import P1 from '../components/p1.svelte';
  import P4 from '../components/p4.svelte';
  import P5 from '../components/p5.svelte';
  import P6 from '../components/p6.svelte';
  import P7 from '../components/p7.svelte';
  import Map from "./Map.svelte";
  import Map_bg from "./Map_bg.svelte";
  import { fade } from 'svelte/transition';

  let data = [];
  // let map_data = [];
  let counter = 0;
  let isOverButton = false;
  let showIntro = true;
  let input_center = [-117.23685272044901, 32.87940699468673];

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
          longitude: +d.longitude,
          quarter: d.quarter
        };
      });
      console.log(data)
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


  function prev(){
    if (counter > 1) {
      counter -= 1;
    }
  }
  function next(){
    if (counter < 6) {
      counter += 1;
    }
  }


  function page1(){
    counter = 1;
  }
  function page2(){
    counter = 2;
  }
  function page3(){
    counter = 3;
  }
  function page4(){
    counter = 4;
  }
  function page5(){
    counter = 5;
  }
  function page6(){
    counter = 6;
  }
  function page7(){
    counter = 7;
  }

  function incrementpage(){
    if (!isOverButton) {
      if (counter < 7) {
        counter += 1
      }
      return;
    }
  }

  function lessenpage(){
    if (!isOverButton) {
      if (counter > 1) {
        counter -= 1
      }
      return;
    }
  }

  if (typeof window !== 'undefined') {
    window.addEventListener('keydown', (event) => {
      // Check if the right arrow key is pressed (key code 39)
      if (event.keyCode === 39) {
        incrementpage();
      }
      // Check if the left arrow key is pressed (key code 37)
      else if (event.keyCode === 37) {
        lessenpage();
      }
    });
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
      <Map_bg {data} {input_center}/>
    </div>
  {:else}
  <div in:fade={{ duration: 250, delay: 1000 }} class = "mainpages">
    <h1>UCSD's Timely Warnings</h1>

    {#if counter < 1}
      <h1>We might not need this page but just in case</h1>
      //maybe go back to intro
    {/if}
  
    {#if counter >= 1}
      <div class="info-box">
        {#if counter===1}
          <P1/>
        {:else if counter ===2}
            <Crime_bd {data} />
        {:else if counter===3}
            <Map {data} />
        {:else if counter===4}
          <P4 {data}/>
        {:else if counter===5}
          <P5 {data} />
        {:else if counter===6}
          <P6/>
        {:else if counter===7}
          <P7/>
        {:else if counter===8}
        
           
        {/if}
      </div>

    <div class= "button-container">
      <button type="button" class="arrow-button" on:click={prev} on:mouseenter={() => isOverButton = true} on:mouseleave={() => isOverButton = false}> &#9664; </button>
      <button type="button" on:click={page1} on:mouseenter={() => isOverButton = true} on:mouseleave={() => isOverButton = false}> Page 1 </button>
      <button type="button" on:click={page2} on:mouseenter={() => isOverButton = true} on:mouseleave={() => isOverButton = false}> Page 2 </button>
      <button type="button" on:click={page3} on:mouseenter={() => isOverButton = true} on:mouseleave={() => isOverButton = false}> Page 3 </button>
      <button type="button" on:click={page4} on:mouseenter={() => isOverButton = true} on:mouseleave={() => isOverButton = false}> Page 4 </button>
      <button type="button" on:click={page5} on:mouseenter={() => isOverButton = true} on:mouseleave={() => isOverButton = false}> Page 5 </button>
      <button type="button" on:click={page6} on:mouseenter={() => isOverButton = true} on:mouseleave={() => isOverButton = false}> Page 6 </button>
      <button type="button" on:click={page7} on:mouseenter={() => isOverButton = true} on:mouseleave={() => isOverButton = false}> Page 7 </button>
      <button type="button" class="arrow-button" on:click={next} on:mouseenter={() => isOverButton = true} on:mouseleave={() => isOverButton = false}> &#9654; </button>
    </div>
    {/if}

  </div>
  {/if}
</main>

<style>

  h1 {
    font-family: 'Roboto', sans-serif;
  }

  .mainpages {
    background-color: #FDB0C0;
    padding: 20px; 
  }

  .info-box {
    background-color: #FFF0F3;
    margin-top: 20px;
    margin-bottom: 50px;
    margin-left: 20px;
    margin-right: 20px;
    padding: 10px;
    border: 2px solid black;
  }

  .intro_page {
    display: flex;
    flex-direction: column;
    justify-content: center;
    align-items: center;
    height: 100vh;
  }

  .intro_text {
    font-size: 2em;
    text-align: center;
    margin-bottom: 20px;
  }

  .button-container {
    display: flex;
    justify-content: center;
    margin-top: 20px; 
  }

  .button-container button {
    margin-right: 5px;
    background-color: #ADD8E6;
    border-radius: 20px;
    font-size: 1em;
    font-family: 'Roboto', sans-serif;
  }

  .button-container button:hover {
    background-color: white;
  }

  .learn_more_button {
    background-color: #FDB0C0;
    color: black;
    border: none;
    border-radius: 25px; 
    padding: 10px 20px; 
    font-size: 1.2em; 
    font-family: 'Roboto', sans-serif;
    cursor: pointer;
  }

  .learn_more_button:hover {
    background-color: #ADD8E6;
  }
</style>