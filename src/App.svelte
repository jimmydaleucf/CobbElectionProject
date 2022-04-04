<script>
  import { onMount } from "svelte";

  export let name;
  let svgMarkup;

  onMount(() => {
    fetch("/cobbStateHouse.svg")
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
      {@html svgMarkup}
    </div>
  {/if}
</main>

<style>
  main {
    text-align: center;
    padding: 1em;
    max-width: 240px;
    margin: 0 auto;
  }

  h1 {
    color: #ff3e00;
    text-transform: uppercase;
    font-size: 4em;
    font-weight: 100;
  }
  .map-container {
    fill: white;
    border-style: solid;
  }

  @media (min-width: 640px) {
    main {
      max-width: none;
    }
  }
</style>
