<script>
  import { onMount } from "svelte";

  export let name;
  let svgMarkup;

  onMount(() => {
    fetch("/cobbStateHouse2020.svg")
      .then((res) => res.text())
      .then((res) => {
        svgMarkup = res;
      });
  });
</script>

<main>
  <h1>Jimmy's Elerction Dershberd</h1>
  {#if svgMarkup}
    <div class="map-container">
      <h3>2020 State House</h3>
      {@html svgMarkup}
    </div>
  {/if}
</main>

<style>
  main {
    text-align: center;
    padding: 1em;
    /* max-width: 240px; */
    /* margin: 0 auto; */
  }

  h1 {
    color: #ff3e00;
    text-transform: uppercase;
    font-size: 4em;
    font-weight: 100;
  }
  .map-container {
    display: flex;
    flex-direction: column;
    fill: white;
    border-style: solid;
    padding: 40px;
    border-style: solid;
    border-color: darkslategray;
    /* box-shadow: 1px -1px 5px darkslategray; */
    margin: 10px;
    min-width: 20%;

    position: absolute;
  }
  @import url("https://fonts.googleapis.com/css2?family=Baloo+Bhaijaan+2&family=Fredoka+One&family=Roboto:ital,wght@0,100;0,400;0,900;1,300;1,700;1,900&display=swap");

  @media (max-width: 480px) {
    main {
      max-width: none;
    }
    .map-container {
      display: flex;
      min-width: 80%;
      padding: 5px;
    }
  }
</style>
