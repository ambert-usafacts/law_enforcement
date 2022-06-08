<script>
  import data from './assets/smallMultiplesTime.csv'
  import {groups, greatest, least, ascending, groupSort, descending} from 'd3-array'
  import { scaleLinear } from 'd3-scale'
  import { fade } from 'svelte/transition';
  import Chart from './lib/Chart.svelte'
  import Toggle from './lib/Toggle.svelte'

  let sliderValue;

  $: sliderEx = sliderValue === 'on' ? 'Showing all agencies' : 'Showing only agencies with big changes'
  


  const jurisdictions = data.map(d => ({
    ori_code: d.ori_code, 
    year: +d.year,
    officers: +d.officers_per_1000,
    slope: +d.estimate,
    direction: +d.estimate > 0 ? 'ascending' : 'descending',
    state: d.ori_code.slice(0, 2),
    agency: d.agency,
    population: +d.population
  }))

  $: toggleData = sliderValue === 'on' ? jurisdictions : jurisdictions.filter(d => d.slope > 0.05 || d.slope < - 0.05)


  $: groupJur = groups(toggleData, d => d.state, d => d.agency)

  $: test = groupSort(toggleData, d => d.state, d => d.agency)
  

  $: yearScale = scaleLinear()
      .domain([
        least(toggleData, (a, b) => ascending(a.year, b.year)).year,
        greatest(toggleData, (a, b) => ascending(a.year, b.year)).year])
      .range([10, 140])

  $: officerScale = scaleLinear()
    .domain([
      least(toggleData, (a, b) => ascending(a.officers, b.officers)).officers,
      greatest(toggleData, (a, b) => ascending(a.officers, b.officers)).officers])
    .range([10, 140])

</script>


<main>
  <h1>Change in police officers per 1,000 people in the population, 1990 - 2020</h1>
  <Toggle bind:value={sliderValue} label="Show all agencies" fontSize={24} design="slider" />
  <p class='ex'>{sliderEx}</p>
 
    {#each groupJur as [state, indiJur]}
       <div class='wrapper'>
          <p>{state}</p>
          <div class='g-state'>
            {#each indiJur as [key, values]}
              <div class='container' out:fade>
                  <Chart {key} {values} xScale = {yearScale} yScale = {officerScale} />
              </div>
            {/each}
          </div>
      </div>
    {/each}

</main>




<style>
  main {
    font-family: Source Sans Pro, sans-serif;
  }
  .wrapper {
    margin: 3rem 2rem;
  }

  p {
    font-size: 20px;
    font-weight: 600;
    font-family: Source Sans Pro, sans-serif;
    margin: 0.25rem;
  }

  .ex {
    font-size: 14px;
    font-weight: 400;
    color: #999;
    margin: 0;
  }

  .g-state{
    display: flex;
    flex-wrap: wrap;
    gap: 10px;
    background-color: #F5F5F5;
    padding: 2rem;
  }

  .container {
    box-shadow: 0px 4px 4px rgb(0 0 0 / 25%);
    background-color: #fff;
    overflow: hidden;
    max-width: 150px;
    width: 150px;
    height:200px;
    position: relative;
  }
</style>
