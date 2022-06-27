<script>
  import Navigation from "./Navigation.svelte";
  import CountyPrecinct from "./CountyPrecinct.svelte";
  export let name;
  let place = "Cobb";
  let keys = [
    { key: "10", label: "Governor" },
    { key: "30", label: "Lt. Governor" },
    { key: "50", label: "Sec. of State" },
    { key: "70", label: "Attorney General" },
  ];
</script>

<main>
  <nav class="navbar">
    <ul>
      {#if keys}
        {#each keys as { key, label }}
          <li>
            <a href="#{key}-{place}-map">{label} </a>
          </li>
        {/each}
      {/if}
    </ul>
  </nav>
  <!-- <Navigation /> -->
  <h1>Cobb County Election Results 2018</h1>
  <div class="container-app">
    {#each keys as { key, label }}
      <CountyPrecinct county={place} raceKey={key} />
    {/each}

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
  a {
    color: white;
    font-size: 1.5em;
  }
  li a:hover {
    color: lightgray;
  }
  ul {
    display: flex;
    justify-content: right;
  }

  li {
    list-style-type: none;
    padding-left: 1em;
    padding-right: 1em;
  }
  ul {
    margin: 0;
  }
  .navbar {
    position: fixed;
    top: 0;
    width: 100%;
    z-index: 1;
    background-color: #0988d7;
    height: 30px;
    overflow: hidden;
  }
  /* .navbar {
    position: fixed;
    top: 0;
    width: 100%;
    z-index: 1;
    height: 30px;
    overflow: hidden;
    width: 100%;
  } */
  @media (max-width: 420px) {
    a {
      font-size: 0.85em;
    }

    li {
      padding: 8px;
    }

    h1 {
      margin-top: 40px;
    }
  }
</style>
