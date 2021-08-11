<script>
    import { LayerCake, Svg, Html } from 'layercake';
  
    import Line from './components/Line.svelte';
    import Area from './components/Area.svelte';
    import AxisX from './components/AxisX.svelte';
    import AxisY from './components/AxisY.svelte';
    import Annotations from './components/Annotations.svelte';
	import Arrows from './components/Arrows.svelte';
	import ArrowheadDef from './components/ArrowheadDef.svelte';

    // This example loads csv data as json using @rollup/plugin-dsv
    let data = [{myX:0,myY:0}, {myX:1000,myY:1000}]
    const xKey = 'myX';
    const yKey = 'myY';
    const annotations = [
		{
			text: 'Example text...',
			top: '28%',
			left: '30%',
			arrows: [{
				clockwise: false, // true or false, defaults to true
				source: {
					anchor: 'left-bottom', // can be `{left, middle, right},{top-middle-bottom}`
					dx: -2,
					dy: -7
				},
				target: {
					x: '28%',
					y: '75%'
				}
			},
			{
				source: {
					anchor: 'right-bottom',
					dy: -7,
					dx: 5
				},
				target: {
					x: '68%',
					y: '48%'
				}
			}]
		}
	];

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
      padding={{ right: 10, bottom: 20, left: 25 }}
      x={xKey}
      y={yKey}
      yDomain={[0, null]}
      data={data}
    >
        <Svg>
            <AxisX/>
            <AxisY
            ticks={4}
            />
            <Line/>
            <Area/>
        </Svg>


    </LayerCake>
  </div>