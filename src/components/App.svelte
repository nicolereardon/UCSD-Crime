<script>
  import P1 from '../components/p1.svelte';
  import P2 from '../components/p2.svelte';

  import * as d3 from 'd3';
  import { onMount } from 'svelte';

  let counter = 1;

  function page1(){
    counter = 0
  }

  function page2(){
    counter = 1
  }

  function incrementpage(){
    if (counter < 2) {
      counter = counter + 1
    }
  }

  $: {
    console.log(counter)
  }
</script>

<main on:click={incrementpage}>
  <h1>DSC106 Final Project Prototype</h1>

  <p>Write your HTML here</p>
  <p>Tada!</p>

  {#if counter===1}
      <P1 />
  {:else if counter===2}
      <P2 />
  {/if}

  <button type="button" on:click={page1}> Page 1</button>
  <button type="button" on:click={page2}> Page 2</button>
</main>

<style>
  /* Write your CSS here */
</style>
