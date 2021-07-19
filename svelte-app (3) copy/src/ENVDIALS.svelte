<script>
    import BAR from './BAR.svelte';
    import COLUMN from './COLUMN.svelte';
    import emissionLookup from './data/consumerEmissionConvertor.csv';

    export let reps;

    let meat = {"Meat in every meal": 40}
    let car = {6: 40}
    let plane = {"Don't usually fly": 0}

    let a = 10;
    let b = meat[reps[0].answerChoice];
    let c = car[reps[1].answerChoice];
    let d = plane[reps[2].answerChoice];
    let e = 1000;

    let energy = {'Electricity': '4.4.1', 'Gas': '4.4.2', 'Oil': '4.4.3.2'};

    function changeA() {
        if (reps[0].answerChoice=="Meat in every meal") {
            console.log('reps', reps)
            a = 30;
            console.log("a inside", a)
        }
    }

    // Transform lad lookup csv to object
    let convertor = {};
    emissionLookup.forEach(d => {convertor[d['Code']] = {"Product": d['Product'], "Ratio": d['GHG (kgCO2e per £)']}});

    console.log("convertor", convertor)

    function data(a, b, c, d, e) {
        return [
            {product: 'Beer', value: 51*a*convertor['2.1.3.1'].Ratio},
            {product: 'Meat', value: 51*b*convertor['1.1.5'].Ratio},
            {product: convertor['7.2.2.1'].Product, value: 51*c*convertor['7.2.2.1'].Ratio},
            {product: convertor['11.2.2'].Product, value: d*convertor['11.2.2'].Ratio},
            {product: convertor[energy[reps[3].answerChoice]].Product, value: e*convertor[energy[reps[3].answerChoice]].Ratio}
        ]
    }

</script>
<div class="profile-grid">
    <div id="cont">
        <div>
            <input type=range bind:value={a} min=0 max=60>
        </div>
        <div>
            £{a}/week on beer = {(+(51*a*convertor['2.1.3.1'].Ratio).toFixed(1)).toLocaleString()}kg
        </div>
    
        <hr>
        
        <div>
            <input type=range bind:value={b} min=0 max=200>
        </div>
        <div>
            £{b}/week on meat = {(+(51*b*convertor['1.1.5'].Ratio).toFixed(1)).toLocaleString()}kg
        </div>
    
        <hr>
    
        <div>
            <input type=range bind:value={c} min=0 max=200>
        </div>
        <div>
            £{c}/week on {(convertor['7.2.2.1'].Product).toLowerCase()} = {(+(51*c*convertor['7.2.2.1'].Ratio).toFixed(1)).toLocaleString()}kg
        </div>
    
        <hr>
    
        <div>
            <input type=range bind:value={d} min=0 max=10000>
        </div>
        <div>
            £{d}/year on {(convertor['11.2.2'].Product).toLowerCase()} = {(+(d*convertor['11.2.2'].Ratio).toFixed(1)).toLocaleString()}kg
        </div>
    
        <hr>
    
        <div>
            <input type=range bind:value={e} min=0 max=5000>
        </div>
        <div>
            £{e}/year on {(convertor[energy[reps[3].answerChoice]].Product).toLowerCase()} = {(+(e*convertor[energy[reps[3].answerChoice]].Ratio).toFixed(1)).toLocaleString()}kg
        </div>
    
        <hr>

        <div style="font-style: italic;">
            kg of GHG emissions per year
        </div>

    </div>

    <div style="height: 700px; margin-bottom: 50px; margin-top: 100px;">
        <COLUMN data={data(a, b, c, d, e)}></COLUMN>
    </div>

</div>

<style>
    #cont {
        width: 60%;
        margin-top: 50px;
        margin-left: auto;
        margin-right: auto;
    }
    hr {
        margin: 40px;
    }
    .profile-grid {
        display:grid;
        width:70%;
        grid-gap:20px 5%;
        grid-template-columns:50% 50%;
        justify-content:stretch;
        margin-left:auto;
        margin-right:auto
    }
</style>
