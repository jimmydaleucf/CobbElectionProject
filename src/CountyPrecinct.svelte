<script>
  import { onMount } from "svelte";
  // import SvelteTooltip from "svelte-tooltip";

  let svgMarkup;
  let precinctData;

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
        const array = svg.querySelectorAll("path");
        for (let i = 0; i < array.length; i++) {
          array[i].classList.add("tooltip");
        }
        svgMarkup = map.innerHTML;
        getResults();
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

  //   var interval = setInterval(getResults, 5000);
</script>

<main>
  <div class="map-container">
    {#if svgMarkup}
      <div class="map">{@html svgMarkup}</div>
    {/if}
  </div>
</main>

<style>
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
</style>
