<script>
  import Navigation from "./Navigation.svelte";
  import CountyPrecinct from "./CountyPrecinct.svelte";
  export let name;
</script>

<main>
  <Navigation />
  <h1>Cobb County Governor Results 2018</h1>
  <div class="container-app">
    <CountyPrecinct county="Cobb" raceKey="10" />
    <CountyPrecinct county="Cobb" raceKey="30" />

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
    font-weight: 150;
    margin: 20px;
    padding-top: 40px;
  }
  @media (min-width: 640px) {
    h1 {
      margin-top: 40px;
    }
  }
</style>
