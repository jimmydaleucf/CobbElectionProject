<script>
  import CountyPrecinct from "./CountyPrecinct.svelte";
  import BurgerMenu from "svelte-burger-menu";

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
    <div class="mobile">
      <BurgerMenu
        width="35%"
        backgroundColor="#2f4f4f"
        burgerColor="#ddc7a0"
        menuColor="#89CFF0"
        ><h2><a href="#home">Home</a></h2>
        {#each keys as { key, label }}
          <h2>
            <a href="#{key}-{place}-map">{label} </a>
          </h2>
        {/each}
      </BurgerMenu>
    </div>
    {#if keys}
      {#each keys as { key, label }}
        <li class="desktop">
          <a href="#{key}-{place}-map">{label} </a>
        </li>
      {/each}
    {/if}
  </nav>
  <!-- <Navigation /> -->
  <h1 id="home">Cobb County Election Results 2018</h1>
  <div class="text">
    <p>Welcome to my page!</p>
    <p>
      As an avid follower of politics, I've always been frustrated that there
      isn't a great site to view precinct level election results for my county.
      I've decided to put my newly developed software engineering skills to work
      to create one. Using the data that is available on the <a
        class="links"
        href="https://sos.ga.gov/page/georgia-election-results"
        target="_blank">Secretary of State's website</a
      >, I have linked the precincts on the map to their respective results.
      These results appear as you hover over each one. I plan to also build a
      site to track live results as they come in on November 8, 2022.
    </p>
    <p>Stay tuned!</p>
  </div>
  <div class="container-app">
    {#each keys as { key, label }}
      <CountyPrecinct county={place} raceKey={key} />
    {/each}

    <div />
  </div>
  <div class="text citation" id="footer">
    Data Source: <a
      class="links"
      href="https://sos.ga.gov/page/georgia-election-results"
      target="_blank">Georgia Secretary of State's Website</a
    >
    <span class="plain"> |</span> 2018 County Precinct Map provided by
    <a
      class="links"
      href="https://geo-cobbcountyga.hub.arcgis.com/"
      target="_blank">Cobb County GIS</a
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
  .mobile {
    display: none;
  }

  .navbar {
    display: flex;
    align-items: flex-end;
    justify-content: right;
    position: fixed;
    top: 0;
    width: 100%;
    z-index: 1;
    background-color: darkslategray;
    min-height: 25px;
    /* overflow: hidden; */
  }
  .text {
    margin-left: 2em;
    margin-right: 2em;
    font-size: 1.25em;
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
  #footer {
    padding-bottom: 20px;
  }

  @media (max-width: 420px) {
    a {
      font-size: 0.95em;
    }
    .mobile {
      display: block;
      padding-right: 10px;
      align-items: flex-end;
      width: 100%;
    }
    .desktop {
      display: none;
    }

    .navbar {
      display: flex;
      align-items: flex-end;
      align-items: right;
      min-height: 50px;
    }
    li {
      padding: 8px;
    }

    h1 {
      margin-top: 40px;
    }
  }
</style>
