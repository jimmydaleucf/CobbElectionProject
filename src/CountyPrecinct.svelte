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
  let raceTitle;
  let raceResults = [];
  let headers = ["Candidate", "Party", "Total Votes"];
  export let county;
  export let raceKey;

  let palette = {
    REP: "#DE8275",
    DEM: "#6495ED",
    LIB: "#E49B0F",
    TIE: "#808080",
  };

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
      svg.setAttribute("id", `${county}-${raceKey}`);
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
          let targetPrecinct = raceResults.find(
            (element) =>
              element.precinct ===
              `${id}` /*This filters the results to only the one with the same precinct name as the path id from the svg. */
          );
          if (targetPrecinct === undefined) {
          } else {
            const newTable = document.createElement("table");
            const thead = document.createElement("thead");
            const tbody = document.createElement("tbody");
            newTable.appendChild(thead);
            newTable.appendChild(tbody);
            for (let i = 0; i < targetPrecinct.candidates.length; i++) {
              const newRow = document.createElement("tr");
              tbody.appendChild(newRow);
              const newCell = document.createElement("td");
              let name = targetPrecinct.candidates[i].name;
              newCell.innerHTML = `${name}`;
              let voteCell = document.createElement("td");
              let numberOfVotes =
                targetPrecinct.candidates[i].votes.toLocaleString();
              voteCell.innerHTML = `${numberOfVotes}`;
              newRow.appendChild(newCell);
              newRow.appendChild(voteCell);
            }
            let precinctInfo = document.getElementById(
              `${raceKey}-${county}-precinct-crm`
            );
            precinctInfo.innerHTML = `<h3>${id} </h3>`;
            precinctInfo.appendChild(newTable);
          }
        });
        node.addEventListener("mouseleave", () => {
          document.getElementById(
            `${raceKey}-${county}-precinct-crm`
          ).innerHTML =
            "<br /><br />Hover over the map to view the precinct level results (tap on mobile)";
        });
      });
    });

  /*Function to grab overview feed and pull out candidate lists and overview vote totals */
  async function getOverview() {
    console.log("running getOverview");
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
      raceTitle = contestOverview.C;
      for (let i = 0; i < candidateList.length; i++) {
        let name = candidateList[i].slice(0, -6);
        let foo = candidateList[i].slice(-5);
        let party = foo.slice(1, 4).toUpperCase();
        const newData = new Object();
        newData.candidate = name;
        newData.voteTotalNum =
          overviewVotes[i]; /*object value used only to sort */
        newData.voteTotal =
          overviewVotes[i].toLocaleString(); /*add thousands separator */
        newData.party = party;
        overviewArray.push(newData);
        overviewArray.sort(function (a, b) {
          return b.voteTotalNum - a.voteTotalNum;
        });
        overviewArray = overviewArray;
        console.log(overviewArray); /*overviewArray is the county totals */
      }
    } else {
      throw new Error(text);
    }
  }

  /** This function fetches the precinct results feed for all the contests */
  async function getResults() {
    console.log("running getResults");
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
      transformData(contestResults);
    } else {
      throw new Error(text);
    }
  }

  /*This function takes the two feeds and transforms them into an array of objects (each precinct results set)*/
  const transformData = (contestResults) => {
    console.log("running transformData");
    let precinctsArray = contestResults.P;
    let votes = contestResults.V;
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
    paintMap(raceResults);
  };

  /*This function paints the map using the unified raceResults JSON that was created in the transform function*/
  const paintMap = (raceResults) => {
    for (let i = 0; i < raceResults.length; i++) {
      const location = raceResults[i];
      const id = raceResults[i].precinct;
      const winner = raceResults[i].candidates[0];
      if (
        winner.votes > 0 &&
        winner.votes !== raceResults[i].candidates[1].votes
      ) {
        //add that vote count is greater than zero. and that there isn't a tie.*/
        const mapInstance = document.getElementById(`${county}-${raceKey}`);
        const mapPrecinct = mapInstance.getElementById(id);
        let winnerColor = palette[winner.party];
        mapPrecinct.style.fill = winnerColor;
      } else if (winner.votes == raceResults[i].candidates[1].votes) {
        const mapInstance = document.getElementById(`${county}-${raceKey}`);
        const mapPrecinct = mapInstance.getElementById(id);
        let winnerColor = palette["TIE"];
        mapPrecinct.style.fill = winnerColor;
      } else {
      }
    }
  };
</script>

<main>
  <h2 id="crm-title">{raceTitle}</h2>
  <div class="map-container" id="{raceKey}-{county}-container">
    {#if svgMarkup}
      <div class="map" id="{raceKey}-{county}-map" bind:this={map}>
        {@html svgMarkup}
      </div>
    {/if}
    <div class="crm" id="{raceKey}-{county}-crm">
      <h3>{county} County Results</h3>
      {#if overviewArray}
        <table>
          <thead>
            {#each headers as header}
              <th class={header}>{header}</th>
            {/each}
          </thead>
          <tbody>
            {#each overviewArray as { candidate, party, voteTotal }}
              <tr
                ><td>{candidate}</td><td style="color:{palette[party]}"
                  >{party}</td
                ><td>{voteTotal}</td></tr
              >{/each}
          </tbody>
        </table>
      {/if}
      <div id="{raceKey}-{county}-precinct-crm" class="precinct-crm">
        <br /><br /> Hover over the map to view the precinct level results (tap on
        mobile)
      </div>
    </div>
  </div>
</main>

<style>
  .map {
    fill: white;
    stroke: black;
    margin: 8px;
    padding-bottom: 20px;
  }
  main {
    margin-top: 20px;
    margin-bottom: 20px;
    margin-left: 5px;
    margin-right: 5px;
    width: 100%;
    box-shadow: 3px 3px 3px 3px grey;
    border-radius: 10px;
    padding: 10px;
    background-color: #e4d5b7;
  }
  .map-container {
    display: block;
    padding-top: 15px;
    padding-bottom: 15px;
  }

  td {
    padding: 5px;
    font-size: 0.85em;
    text-align: center;
  }

  #crm-title {
    font-size: 2em;
    margin: 0px;
    padding-top: 25px;
    padding: 5px;
  }
  .precinct-crm {
    padding-top: 0px;
    display: flex;
    flex-direction: column;
    font-size: 0.85em;
    font-style: italic;
  }

  .crm {
    background-color: white;
    margin: 10px;
    padding: 10px;
    display: flex;
    flex-direction: column;
    max-height: auto;
    font-size: 1.25em;
    border-radius: 3%;
    box-shadow: 5px 5px 5px #555;
  }

  @media (min-width: 640px) {
    main {
      /* margin : auto; */
      max-width: 45%;
      margin: 20px;
    }
    .map {
      width: 40%;
      margin: 15px;
      padding-bottom: 0px;
    }
    .map-container {
      min-height: 420px;
      display: flex;
      justify-content: center;
      align-items: center;
      padding-top: 5px;
      padding-bottom: 0px;
      margin: 0;

      /* align-items: center; */
    }
    .crm {
      font-size: 1.25em;
      flex-wrap: wrap;
      width: 55%;
    }
    .precinct-crm {
      justify-content: center;
    }
  }
</style>
