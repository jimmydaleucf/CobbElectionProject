<script>
  import { onMount } from "svelte";
  // import SvelteTooltip from "svelte-tooltip";

  let svgMarkup;
  let precinctData;
  let countySummary;
  let candidateList = {};
  let map;
  let totalVotes;
  let headers = ["Candidate", "Total Votes"];
  export let county;
  let raceKey = "10";

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
        getOverview();
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
            ).innerHTML = `<table><h3>${precinctName} </h3><tr><th>Candidate</th><th>Votes</th></tr>
              <tr><td>${sorted[1][0]}</td><td>${sorted[1][1]}</td></tr>
              <tr><td>${sorted[2][0]}</td><td>${sorted[2][1]}</td></tr>
              <tr><td>${sorted[3][0]}</td><td>${sorted[3][1]}</td></tr>
              </table>`;
          });
          node.addEventListener("mouseleave", () => {
            document.getElementById("precinct-crm").innerHTML =
              "<em><br /><br />Hover over the map to view the precinct level results (tap on mobile)</em>";
          });
        });
      });
  });

  /*Function to grab overview feed and pull out candidate lists */
  async function getOverview() {
    const response = await fetch(
      "https://results.enr.clarityelections.com/GA/Cobb/91673/222156/json/sum.json?1655850344398"
    );
    const overview = await response.json();
    if (response.ok) {
      countySummary = overview;
      let contestOverview = countySummary.Contests.find(
        (element) => element.K === `${raceKey}`
      );
      candidateList = contestOverview.CH;
      // console.log(candidateList);
    } else {
      throw new Error(text);
    }
  }

  /****************/

  async function getResults() {
    const res = await fetch(
      `https://results.enr.clarityelections.com/GA/Cobb/91673/222156/json/details.json?1655858295886`
    );
    const results = await res.json();
    if (res.ok) {
      //   debugger;
      precinctData = results;
      let allContestArray = precinctData.Contests;
      // console.log(allContestArray);
      let contestResults = allContestArray.find(
        (element) => element.K === `${raceKey}`
      );
      console.log(contestResults);
      // totalVotes = totalVotes;
      paintMap(contestResults);
    } else {
      throw new Error(text);
    }
  }

  const paintMap = (contestResults) => {
    getOverview();
    // console.log(candidateList);
    let precinctsArray = contestResults.P;
    // console.log(precinctsArray);
    let votes = contestResults.V;
    // console.log(votes);
    for (let i = 0; i < precinctsArray.length; i++) {
      let precinct = precinctsArray[i];
      let votesArray = [votes[i]];
      // console.log("votes array");
      // console.log(votesArray.length);
      for (let i = 0; i < votesArray[0].length; i++) {
        let name = candidateList[i];
        let total = votesArray[0][i];
        let foo = name.slice(-5);
        let party = foo.slice(1, 4);
        console.log(precinct);
        const candidateObj = new Object();
        candidateObj.name = name;
        candidateObj.votes = total;
        candidateObj.party = party;
        console.log(candidateObj);
      }
    }

    // if (kemp > abrams) {
    //   document.getElementById(`${precinct}`).style.fill = "#ec7c71";
    // } else if (kemp < abrams) {
    //   document.getElementById(`${precinct}`).style.fill = "#1AA7EC";
    // } else if (kemp == abrams) {
    //   document.getElementById(`${precinct}`).style.fill = "gray";
    // }
  };
  // };
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
      <div id="precinct-crm" class="">
        <em
          ><br /><br />Hover over the map to view the precinct level results
          (tap on mobile)</em
        >
      </div>
    </div>
  </div>
</main>

<style>
  .map {
    fill: white;
    stroke: black;
  }
  main {
    margin: auto;
    width: 100%;
  }
  .map-container {
    display: block;
  }

  td {
    padding: 5px;
  }

  #precinct-crm {
    padding-top: 0px;
    display: flex;
    flex-direction: column;
    font-size: 0.85em;
    font-style: italic;
  }

  .crm {
    background-color: white;
    margin: 25px;
    padding: 10px;
    display: flex;
    flex-direction: column;
    /* justify-content: center; */
    max-height: auto;
    font-size: 1.2em;
    border-radius: 10%;
    box-shadow: 5px 5px 5px #555;
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
      justify-content: center;
      /* align-items: center; */
    }
    .crm {
      width: 300px;
      min-height: 250px;
      font-size: 1.25em;
      flex-wrap: wrap;
    }
    .precinct-crm {
      justify-content: center;
      padding: 15px;
      margin: 25px;
    }
  }
</style>
