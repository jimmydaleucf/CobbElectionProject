<script>
  import { onMount } from "svelte";
  // import SvelteTooltip from "svelte-tooltip";

  let svgMarkup;
  let precinctData;
  let map;
  let totalVotes;
  let headers = ["Candidate", "Total Votes"];
  export let county;

  onMount(() => {
    fetch("./assets/2018/Cobb.svg")
      .then((res) => res.text())
      .then((res) => {
        svgMarkup = res;
        let map = document.createElement("div");
        map.innerHTML = svgMarkup;
        let svg = map.querySelector("svg");
        svg.classList.add("svg-map");
        svg.setAttribute("height", "100%");
        svg.setAttribute("width", "100%");
        svg.setAttribute("font-size", "3em");
        svg.setAttribute("style", "max-height:400px");
        // const array = svg.querySelectorAll("path");
        svgMarkup = map.innerHTML;
        getResults();
      })
      .then(() => {
        /*Here we add the event listners for the hover to provide precinct data */
        map.querySelectorAll("path").forEach((node) => {
          node.addEventListener("mouseenter", () => {
            let id =
              node.getAttribute(
                "id"
              ); /*This grabs the precinct from the id field in the svg path */
            let thing = precinctData.filter((obj) => {
              /*This filters the results to only the one with the same precicnt name as the path id from the svg. */
              return obj.Precinct === `${id}`;
            });
            let thingObj =
              thing[0]; /* Here we pull out the first (and only) object in the results that matched our filter*/
            let sorted = Object.entries(thingObj).sort(
              (a, b) => b[1] - a[1]
            ); /*This sorts the results so that the candidate with the most votes is first and so on. */
            let precinctName =
              sorted[0][1]; /*This grabs the value that is the precinct name */
            document.getElementById(
              "precinct-crm"
            ).innerHTML += `<table><h3>${precinctName} </h3><tr><th>Candidate</th><th>Votes</th></tr>
              <tr><td>${sorted[1][0]}</td><td>${sorted[1][1]}</td></tr>
              <tr><td>${sorted[2][0]}</td><td>${sorted[2][1]}</td></tr>
              <tr><td>${sorted[3][0]}</td><td>${sorted[3][1]}</td></tr>
              </table>`;
          });
          node.addEventListener("mouseleave", () => {
            document.getElementById("precinct-crm").innerHTML = "";
          });
        });
      });
  });

  async function getResults() {
    const res = await fetch(`./electionResults/cobb_results_gov_2018.json`);
    const results = await res.json();
    if (res.ok) {
      //   debugger;
      precinctData = results;
      totalVotes = precinctData.slice(-1)[0];
      console.log(totalVotes);
      // totalVotes = totalVotes;
      paintMap(precinctData);
    } else {
      throw new Error(text);
    }
  }

  const paintMap = () => {
    for (let i = 0; i < precinctData.length - 1; i++) {
      let precinct = precinctData[i].Precinct;
      let kemp = Number(precinctData[i].Kemp);
      let abrams = Number(precinctData[i].Abrams);

      if (kemp > abrams) {
        document.getElementById(`${precinct}`).style.fill = "#ec7c71";
      } else if (kemp < abrams) {
        document.getElementById(`${precinct}`).style.fill = "#1AA7EC";
      } else if (kemp == abrams) {
        document.getElementById(`${precinct}`).style.fill = "gray";
      }
    }
  };
</script>

<main>
  <div class="map-container">
    {#if svgMarkup}
      <div class="map" bind:this={map}>{@html svgMarkup}</div>
    {/if}
    <div class="crm">
      <h3>{county} County Results</h3>
      {#if totalVotes}
        <table>
          <thead>
            {#each headers as header}
              <th class={header}>{header}</th>
            {/each}
          </thead>
          <tr><td>Abrams</td><td>{totalVotes.Abrams}</td></tr>
          <tr><td>Kemp</td><td>{totalVotes.Kemp}</td></tr>
          <tr><td>Metz</td><td>{totalVotes.Metz}</td></tr>
        </table>
      {/if}
      <div id="precinct-crm" class="" />
    </div>
  </div>
</main>

<style>
  .map {
    fill: white;
    stroke: black;
    padding: 2em;
  }
  main {
    margin: auto;
  }
  .map-container {
    display: block;
  }

  table {
    padding: 5px;
  }

  td {
    padding: 5px;
  }

  #precinct-crm {
    padding: 15px;
    padding-top: 0px;
    margin: 25px;
    display: flex;
    flex-direction: column;
    justify-content: center;
  }

  .crm {
    background-color: white;
    padding: 15px;
    margin: 25px;
    display: flex;
    flex-direction: column;
    justify-content: center;
  }

  /*TOOL TIP CSS*/
  .tooltip {
    position: relative;
    display: inline-block;
    border-bottom: 1px dotted black;
  }

  .tooltip .tooltiptext {
    visibility: hidden;
    width: 120px;
    background-color: #555;
    color: #fff;
    text-align: center;
    border-radius: 6px;
    padding: 5px 0;
    position: absolute;
    z-index: 1;
    bottom: 125%;
    left: 50%;
    margin-left: -60px;
    opacity: 0;
    transition: opacity 0.3s;
  }

  .tooltip .tooltiptext::after {
    content: "";
    position: absolute;
    top: 100%;
    left: 50%;
    margin-left: -5px;
    border-width: 5px;
    border-style: solid;
    border-color: #555 transparent transparent transparent;
  }

  .tooltip:hover .tooltiptext {
    visibility: visible;
    opacity: 1;
  }

  @media (min-width: 640px) {
    .map {
      padding: 2em;
      height: 400px;
    }
    .map-container {
      display: flex;
      align-items: center;
    }
    .crm {
      width: 300px;
      min-height: 250px;
      font-size: 1.25em;
      flex-wrap: wrap;
    }
  }
</style>
