<script>
    import COLUMN from './COLUMN.svelte';
    import emissionLookup from './data/consumerEmissionConvertor.csv';
    import { LineChart } from '@onsvisual/svelte-charts';
    import dataFake from './data';

    export let data;
    console.log(data)

    let carRatio = 0.16496 * 50 / 1.6 // kg CO2e per km * km/hr / occupany rate
    let car = {"Under 2 hours": 1 * carRatio, "2 to 5 hours": 3.5 * carRatio, "5 to 15 hours": 10 * carRatio, "15 to 25 hours": 20 * carRatio, "Over 25 hours": 35 * carRatio}
    let plane = {1:1,2:2,3:3,4:4,5:5,6:6}
    let heat = {"Gas": 3, "Oil": 4, "Electricity": 1, "Wood": 2}
    let commute = 0
    let home = 0

    let a = 9.9;
    let share = 0
    // let b = plane[openReps[1].answerChoice];
    // let c = heat[openReps[2].answerChoice];

    // // function data(a, b, c) {
    //     return [
    //         {product: 'car', value: a},
    //         {product: 'plane', value: b},
    //         {product: 'heat', value: c},
    //     ]
    // }

    // console.log("data(a, b, c)", data(a, b, c))

</script>
<p>If 40% of thw country worked from home {3} days a week it would have x result on UK carbon emissions </p>

<p>{data[11].answerChoice}</p>
<p>{data[14].answerChoice}</p>

    <div class="profile-grid">
        <div id="cont">
            <p>How many homes have green energy</p>
            <p>The average daily car commute is 9.9 miles each way
            </p>
            <div>
                <input type=range bind:value={a} min=0 max=200>
            </div>
            <div>
                Driving <strong>{a} miles</strong> takes on average 30 minutes to complete.
            </div>
            <div>
                Once the return journey is complete 0.1 tonnes of GHG will have been emitted.
            </div>

            <div id="span-cont">
                <span style="float:left; margin-top: 0px;">Sharing your journey with</span>
                <form style="float:left">&nbsp
                    <input type=number step="0.5" bind:value={share}>
            &nbsp</form>
                <span style="margin: revert;">others, you will emit 16 tonnes of GHG in typical year of 240 working days.</span>
            </div>

            <div id="span-cont">

                <span style="float:left; margin-top: 0px;">Living in a</span>

                <form style="float:left">&nbsp
                    <input type=number step="0.5" bind:value={share}>
            &nbsp</form>

                <span style="float:left">bedroom house of</span>

                <form style="float:left">&nbsp
                    <input type=number step="0.5" bind:value={share}>
            &nbsp</form>

                <span style="margin: revert;">residents reduces your household energy consumption by 20%, compared to a house of 3.</span>
            </div>

            <div>
                <input type=range bind:value={a} min=0 max=200>
            </div>

        </div>
        
        <div>
            <h3>An average person would save 2 tonnes of CO2 a year if they spent two hours a week less in a car</h3>
            <div class="chart">
                <LineChart data={dataFake} xKey="year" yKey="value" zKey="group" stacked={true} line={false} areaOpacity={1}/>
            </div>
        </div>
    </div>

    <!-- 
        <div>
            <input type=range bind:value={b} min=0 max=10000>
        </div>
        <div>
{b}
        </div>
    
        <hr>
    
        <div>
            <input type=range bind:value={c} min=0 max=5000>
        </div>
        <div>
{c}
        </div>
    
        <hr>

        <div style="font-style: italic;">
            kg of GHG emissions per year
        </div>

    </div>

    <div style="height: 700px; margin-bottom: 50px; margin-top: 100px;">
        <COLUMN data={data(a, b, c)}></COLUMN>
    </div>

</div>
-->
<style>
h1, h2, h3, h4, h5 {
    width: 500px;
}    
.chart {
        height: 250px;
        width: 500px;
        margin-right: auto;
        margin-left: auto;
        margin-bottom:  20px;
    }
@media screen and (min-width: 960px) {
    .chart {
        margin-left: revert;
    }
}    
    #cont {
        width: 90%;
        margin-top: 50px;
        margin-left: auto;
        margin-right: auto;
    }
    div {
        margin-top: 50px
    }
    span {
        line-height: 1.8
    }
    #span-cont {
        margin-top: 50px
    }
    input[type="number"] {
        width: 40px;
        text-align: center;
        padding-left: 2px;
        border-width: inherit;
        border-bottom-width: thin;
    }
    hr {
        margin: 40px;
    }
    .profile-grid {
            width:70%;
            grid-gap:20px 5%;
            grid-template-columns:50% 50%;
            justify-content:stretch;
            margin-left:auto;
            margin-right:auto
        }

    @media screen and (min-width: 960px) {
        .profile-grid {
            display:grid;
        }
    }
</style> 
