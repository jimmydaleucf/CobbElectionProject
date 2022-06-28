<script>
  import CountyPrecinct from "./CountyPrecinct.svelte";
  export let name;
  let place = "Cobb";
  let keys = [
    { key: "10", label: "Governor" },
    { key: "30", label: "Lt. Governor" },
    { key: "50", label: "Sec. of State" },
    { key: "70", label: "Attorney General" },
    { key: "200", label: "U.S. Rep 6" },
    { key: "220", label: "U.S. Rep 11" },
    { key: "230", label: "U.S. Rep 13" },
    { key: "240", label: "State Sen 6" },
  ];
</script>

<main>
  <nav class="navbar">
    {#if keys}
      {#each keys as { key, label }}
        <li>
          <a href="#{key}-{place}-map">{label} </a>
        </li>
      {/each}
    {/if}
  </nav>
  <!-- <Navigation /> -->
  <h1>Cobb County Election Results 2018</h1>
  <div class="text">
    Lorem ipsum dolor sit amet, consectetur adipiscing elit. Integer nunc nunc,
    tristique sed pulvinar sed, pellentesque vel nibh. In molestie a ligula vel
    efficitur. Curabitur non justo a est accumsan eleifend. Sed pulvinar auctor
    erat, sed scelerisque turpis suscipit quis. Donec volutpat rutrum consequat.
    Nulla sodales mi semper, vulputate ex vel, commodo purus. Pellentesque nunc
    dolor, ultricies ut posuere posuere, mattis a tortor. Integer a gravida
    odio, in scelerisque ex. Suspendisse potenti. Phasellus volutpat, mi sed
    volutpat aliquet, tortor magna elementum quam, cursus sollicitudin urna
    nulla ac mauris. Aenean imperdiet molestie sem. Curabitur porta id mauris
    nec tempus. Sed mi ipsum, scelerisque et felis non, tristique bibendum diam.
    Lorem ipsum dolor sit amet, consectetur adipiscing elit. Aenean fringilla
    ligula urna, nec faucibus tortor molestie at. Sed maximus convallis risus
    nec aliquet. Curabitur ultricies vulputate suscipit. Pellentesque mattis
    rutrum turpis, dapibus pharetra magna pulvinar sit amet. Nullam varius nisl
    quis nisi eleifend accumsan. Nullam non pellentesque nisi. In condimentum
    sollicitudin justo, consectetur tempor dolor pharetra nec. Fusce cursus ex
    et eros placerat ultrices. Suspendisse nunc diam, dapibus sit amet varius
    at, suscipit ac lorem. Sed vestibulum semper nisi ornare lobortis.
  </div>
  <div class="container-app">
    {#each keys as { key, label }}
      <CountyPrecinct county={place} raceKey={key} />
    {/each}

    <div />
  </div>
  <div class="text citation">
    Data Source: <a
      class="links"
      href="https://sos.ga.gov/page/georgia-election-results"
      >Georgia Secretary of State's Website</a
    >
    <span class="plain"> |</span> 2018 County Precinct Map provided by
    <a class="links" href="https://geo-cobbcountyga.hub.arcgis.com/"
      >Cobb County GIS</a
    >
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
    font-size: 1.25em;
  }
  li a:hover {
    color: lightgray;
  }
  ul {
    display: flex;
    justify-content: right;
    margin: 0;
  }

  li {
    list-style-type: none;
    padding-left: 1em;
    padding-right: 1em;
    justify-content: center;
  }
  ul {
  }
  .navbar {
    display: flex;
    align-items: flex-end;
    justify-content: right;
    position: fixed;
    top: 0;
    width: 100%;
    z-index: 1;
    background-color: #0988d7;
    min-height: 25px;
    overflow: hidden;
  }
  .text {
    padding: 2em;
    margin-left: 2em;
    margin-right: 2em;
  }
  .plain {
    font-style: normal;
  }
  .links {
    color: #0f52ba;
    font-size: 1em;
    text-decoration: underline;
  }
  .citation {
    font-style: italic;
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
      font-size: 0.95em;
    }
    ul {
    }
    li {
      padding: 8px;
    }

    h1 {
      margin-top: 40px;
    }
  }
</style>
