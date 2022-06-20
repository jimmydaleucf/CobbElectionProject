<script>
  import { onMount } from "svelte";
  // import SvelteTooltip from "svelte-tooltip";

  let svgMarkup;
  let precinctData;
  let hoverColor = "blue";
  let hoverText = "";
  let fillColor = "white";
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
        svg.setAttribute("style", "max-height:300px");
        // const array = svg.querySelectorAll("path");

        svgMarkup = map.innerHTML;
        getResults();
      })
      .then(() => {
        map.querySelectorAll("path").forEach((node) => {
          node.addEventListener("mouseenter", () => {
            let id = node.getAttribute("id");
            document.querySelector("button").innerText = id;
          });
          node.addEventListener("mouseleave", () => {
            document.querySelector("button").innerText = "PLACEHOLDER FOR CRM";
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
      let Kemp = Number(precinctData[i].Kemp);
      let Abrams = Number(precinctData[i].Abrams);

      if (Kemp > Abrams) {
        document.getElementById(`${precinct}`).style.fill = "#ec7c71";
      } else if (Kemp < Abrams) {
        document.getElementById(`${precinct}`).style.fill = "#1AA7EC";
      } else if (Kemp == Abrams) {
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
  </div>

  <button> PLACEHOLDER FOR CRM </button>
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
</style>
