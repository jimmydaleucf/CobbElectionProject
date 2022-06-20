<script>
  import Navigation from "./Navigation.svelte";
  import CountyPrecinct from "./CountyPrecinct.svelte";
  export let name;
</script>

<main>
  <Navigation />
  <h1>Georgia Governor 2018</h1>
  <div class="container-app">
    <CountyPrecinct county="Cobb" />

    <div />
  </div>
</main>

<style>
  main {
    text-align: center;
  }
  .container-app {
    display: flex;
    flex-wrap: wrap;
    justify-content: center;
  }

  h1 {
    color: darkslategray;
    text-transform: uppercase;
    font-size: 4em;
    font-weight: 100;
  }
</style>
