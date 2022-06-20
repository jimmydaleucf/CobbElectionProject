<script>
  import { onMount } from "svelte";
  // import SvelteTooltip from "svelte-tooltip";

  let svgMarkup;
  let precinctData;
  let map;

  onMount(() => {
    fetch("./Cobb-2018.svg")
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
        map.querySelectorAll("path").forEach((node) => {
          node.addEventListener("mouseenter", () => {
            let id = node.getAttribute("id");

            let thing = precinctData.filter((obj) => {
              return obj.Precinct === `${id}`;
            });
            document.getElementById(
              "crm"
            ).innerHTML = `<table><h2>${id}</h2><tr><th>Candidate</th><th>Votes</th></tr>
              <tr><td>Stacey Abrams</td><td>${thing[0].Abrams}</td></tr>
              <tr><td>Brian Kemp</td><td>${thing[0].Kemp}</td></tr>
              </table>`;
          });
          node.addEventListener("mouseleave", () => {
            document.getElementById("crm").innerText = "Precinct Name";
          });
        });
      });
  });

  async function getResults() {
    const res = await fetch(`./cobb_results_gov_2018.json`);
    const results = await res.json();
    if (res.ok) {
      //   debugger;
      precinctData = results;
      paintMap(precinctData);
    } else {
      throw new Error(text);
    }
  }

  const paintMap = () => {
    for (let i = 0; i < precinctData.length; i++) {
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
    <div id="crm">Precinct Name</div>
  </div>
</main>

<style>
  .tester {
    background-color: white;
    min-height: 50px;
    margin: auto;
    border-style: solid;
    border-width: 1px;
    border-color: black;
  }

  .map {
    fill: white;
    stroke: black;
  }
  main {
    margin: auto;
  }
  .map-container {
  }

  svg {
  }
  #crm {
    background-color: white;
  }

  @media (min-width: 640px) {
    .map {
      padding: 2em;
    }
  }
  /* 
  .tooltip:hover {
    background-color: green;
  } */
  .gop {
    fill: #ec7c71;
  }
  .gop:hover {
    fill: green;
  }
  .dem {
    fill: #1aa7ec;
  }
  .tie {
    fill: gray;
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
</style>
