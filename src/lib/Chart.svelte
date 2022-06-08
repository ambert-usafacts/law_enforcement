<script>
    import { line } from 'd3-shape'
  
    import {least, greatest, sort, ascending} from 'd3-array'

    export let key;
    export let values;
    export let xScale;
    export let yScale;

    $: ordered = values.sort((a, b) => ascending(a.year, b.year))

    let lineGenerator = line()
        .x(d => xScale(d.year) )
        .y(d => yScale(d.officers))

    $: lastYear = ordered[ordered.length - 1]
    $: bigChange = ordered[0].slope > 0.05 || ordered[0].slope < -0.05 ? true : false;

</script>

<div>
   <p>{ordered[0].agency}</p> 
</div>

<svg class:bigChange >
    <g>
       <path d={lineGenerator(ordered)} class={ordered[0].direction}/> 
       <circle cx = {xScale(lastYear.year)} cy = {yScale(lastYear.officers)} r = 4 class={ordered[0].direction}/>
    </g>
</svg>

<p class='pop'>{ordered[0].population} people</p>

<style>
    div {
        background-color: white;
        margin: 0;
        padding: 5px 0;
        border-bottom: 1px solid #E0E0E0;
    }
    p {
        font-size: 16px;
        font-weight: 600;
        text-align: center;
        font-family: Source Sans Pro, sans-serif;
        margin: 0.25rem;
        text-transform: capitalize;
    }

    p.pop {
        font-size: 14px;
        color: #9c9c9c;
        position: absolute;
        bottom: 0;
        left: 0;
        font-weight: 400;
        text-transform: lowercase;
    }

    svg {
        width: 100%;
        max-height: 150px;
        min-height: 150px;
        opacity: 0.1;
    }

    svg.bigChange { 
        opacity: 1
    }

    g {
        margin: 2rem;
    }

    path {
        fill: none;
        stroke-width: 3px;
    }

    path.ascending {
        stroke: #00347b;
    }

    path.descending{
        stroke: #e62790;
    } 

    circle.ascending { 
        fill: #00347b;
    }

    circle.descending{
        fill: #e62790;
    }

    text {
        font-family: Source Sans Pro, sans-serif;
        color: #ccc;
    }
</style>