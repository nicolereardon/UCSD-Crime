<script>
  import P1 from '../components/p1.svelte';
  import P2 from '../components/p2.svelte';
  import Map from "./Map.svelte";

  let counter = 1;
  let isOverButton = false;

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

  <p>Write your HTML here</p>
  <a href="Writeup.html" target="_blank"> Click here for Writeup!</a>


  <div class="info-box">
    {#if counter===1}
        <P1 />
    {:else if counter===2}
        <P2 />
    {:else if counter===3}
      <div>
        <Map /> 
      </div>
    {/if}
  </div>

  <button type="button" on:click={page1} on:mouseenter={() => isOverButton = true} on:mouseleave={() => isOverButton = false}> Page 1</button>
  <button type="button" on:click={page2} on:mouseenter={() => isOverButton = true} on:mouseleave={() => isOverButton = false}> Page 2</button>
  <button type="button" on:click={page3} on:mouseenter={() => isOverButton = true} on:mouseleave={() => isOverButton = false}> Page 3</button>
</main>

<style>
  /* Write your CSS here */
  .info-box {
    margin-top: 20px;
    margin-bottom: 50px;
    margin-left: 20px;
    margin-right: 20px;
    padding: 10px;
    border: 1px solid #ccc;
  }
</style>