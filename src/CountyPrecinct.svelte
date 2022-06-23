<script>
  import { onMount } from "svelte";
  // import SvelteTooltip from "svelte-tooltip";

  let svgMarkup;
  let precinctData;
  let countySummary;
  let candidateList = {};
  let map;
  let overviewArray = [];
  let overviewVotes;
  let raceResults = [];
  let headers = ["Candidate", "Total Votes"];
  export let county;
  export let raceKey;

  let palette = { REP: "#DE8275", DEM: "#6495ED", OTHER: "#FDDA0D" };

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
        svgMarkup = map.innerHTML;
        getOverview();
        getResults();
      })
      .then(() => {
        /*Here we add the event listners for the hover to provide precinct data */
        map.querySelectorAll("path").forEach((node) => {
          node.addEventListener("mouseenter", () => {
            let id = node.getAttribute(
              /*This grabs the precinct from the id field in the svg path */
              "id"
            );
            console.log(id);
            let targetPrecinct = raceResults.find(
              (element) => element.precinct === `${id}`
            );
            /*This filters the results to only the one with the same precinct name as the path id from the svg. */
            console.log(targetPrecinct);
            console.log;
            const newTable = document.createElement("table");
            const thead = document.createElement("thead");
            const tbody = document.createElement("tbody");
            newTable.appendChild(thead);
            newTable.appendChild(tbody);
            console.log(targetPrecinct.candidates.length);
            for (let i = 0; i < targetPrecinct.candidates.length; i++) {
              const newRow = document.createElement("tr");
              tbody.appendChild(newRow);
              const newCell = document.createElement("td");
              let name = targetPrecinct.candidates[i].name;
              newCell.innerHTML = `${name}`;
              let voteCell = document.createElement("td");
              let numberOfVotes = targetPrecinct.candidates[i].votes;
              voteCell.innerHTML = `${numberOfVotes}`;
              newRow.appendChild(newCell);
              newRow.appendChild(voteCell);
            }
            let precinctInfo = document.getElementById("precinct-crm");
            precinctInfo.innerHTML = `<h3>${id} </h3>`;
            precinctInfo.appendChild(newTable);
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
      overviewVotes = contestOverview.V;
      for (let i = 0; i < candidateList.length; i++) {
        let name = candidateList[i].slice(0, -6);
        let foo = candidateList[i].slice(-5);
        let party = foo.slice(1, 4).toUpperCase();
        const jimmy = new Object();
        jimmy.candidate = name;
        jimmy.voteTotal = overviewVotes[i];
        jimmy.party = party;
        overviewArray.push(jimmy);
        overviewArray = overviewArray;
      }
    } else {
      throw new Error(text);
    }
  }

  /** This function fetches the precinct results feed for all the contests */
  async function getResults() {
    const res = await fetch(
      `https://results.enr.clarityelections.com/GA/Cobb/91673/222156/json/details.json?1655858295886`
    );
    const results = await res.json();
    if (res.ok) {
      precinctData = results;
      let allContestArray = precinctData.Contests;
      let contestResults = allContestArray.find(
        (element) => element.K === `${raceKey}`
      );
      // console.log(contestResults);
      transformData(contestResults);
    } else {
      throw new Error(text);
    }
  }

  /*This function takes the two feeds and transforms them into an array of objects (each precinct results set)*/
  const transformData = (contestResults) => {
    let precinctsArray = contestResults.P;
    let votes = contestResults.V;
    // console.log(contestResults);
    for (let i = 0; i < precinctsArray.length; i++) {
      let precinct = precinctsArray[i];
      let votesArray = [votes[i]];
      let resultsArray = [];
      for (let i = 0; i < votesArray[0].length; i++) {
        let name = candidateList[i].slice(0, -6);
        let total = votesArray[0][i];
        let foo = candidateList[i].slice(-5);
        let party = foo.slice(1, 4).toUpperCase();
        const candidateObj = new Object();
        candidateObj.name = name;
        candidateObj.votes = total;
        candidateObj.party = party;
        resultsArray.push(candidateObj);
      }
      let precinctObj = new Object();
      precinctObj.precinct = precinct;
      precinctObj.candidates = resultsArray;
      resultsArray.sort(function (a, b) {
        return b.votes - a.votes;
      });
      raceResults.push(precinctObj);
    }
    let newObj = new Object();
    newObj.candidates = candidateList;
    newObj.precincts = raceResults;
    console.log(overviewArray);

    paintMap(raceResults);
  };

  /*This function paints the map using the unified raceResults JSON that was created in the transform function*/
  const paintMap = (raceResults) => {
    for (let i = 0; i < raceResults.length; i++) {
      const location = raceResults[i];
      const id = raceResults[i].precinct;
      const winner = raceResults[i].candidates[0]; //add that vote count is greater than zero.
      if (winner.votes > 0) {
        const mapInstance = document.querySelector("svg");
        const mapPrecinct = mapInstance.getElementById(id);
        let winnerColor = palette[winner.party];
        mapPrecinct.style.fill = winnerColor;
      } else {
      }
    }
  };
</script>

<main>
  <div class="map-container">
    {#if svgMarkup}
      <div class="map" id={raceKey} bind:this={map}>{@html svgMarkup}</div>
    {/if}
    <div class="crm">
      <h3>{county} County Results</h3>
      {#if overviewArray}
        <table>
          <thead>
            {#each headers as header}
              <th class={header}>{header}</th>
            {/each}
          </thead>
          <tbody>
            {#each overviewArray as { candidate, voteTotal }}
              <tr><td>{candidate}</td><td>{voteTotal}</td></tr>{/each}
          </tbody>
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
    font-size: 0.85em;
    text-align: center;
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
    max-height: auto;
    font-size: 1.2em;
    border-radius: 10%;
    box-shadow: 5px 5px 5px #555;
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
