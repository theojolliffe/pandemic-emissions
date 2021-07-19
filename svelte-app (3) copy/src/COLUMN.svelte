<script>
    import { LayerCake, Svg, Html } from 'layercake';
    import { scaleBand } from 'd3-scale';
  
    import Column from './components/Column.svelte';
    import AxisX from './components/AxisX.svelte';
    import AxisY from './components/AxisY.svelte';
  
    // This example loads csv data as json using @rollup/plugin-dsv
    import dataold from './data/groups.csv';
    console.log('datold', dataold)

    export let data;

    console.log("data", data)
  
    const xKey = 'product';
    const yKey = 'value';
  
    data.forEach(d => {
      d[yKey] = +d[yKey];
    });
  </script>
  
  <style>
    /*
      The wrapper div needs to have an explicit width and height in CSS.
      It can also be a flexbox child or CSS grid element.
      The point being it needs dimensions since the <LayerCake> element will
      expand to fill it.
    */
    .chart-container {
      width: 100%;
      height: 100%;
    }
  </style>
  
  <div class="chart-container">
    <LayerCake
      padding={{ top: 0, right: 0, bottom: 20, left: 20 }}
      x={xKey}
      y={yKey}
      xScale={scaleBand().paddingInner([0.02]).round(true)}
      xDomain={[data[0].product, data[1].product, data[2].product, data[3].product, data[4].product]}
      yDomain={[0, null]}
      data={data}
    >
      <Svg>
        <Column/>
        <AxisX
          gridlines={false}
        />
        <AxisY
          gridlines={false}
        />
      </Svg>
  
    </LayerCake>
  </div>